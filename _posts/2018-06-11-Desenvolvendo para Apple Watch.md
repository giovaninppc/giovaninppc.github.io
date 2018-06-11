---
layout: post
title: "Desenvolvendo para Apple Watch"
featured-img: SMCA/ac2
categories: [Guides, pt-BR]
---

# Desenvolvendo apps para Apple Watch

Na última semana eu fiz a versão do Super Mini Color Arcade para Apple Watch, foi o primeiro app que publiquei para Watch apesar de ja ter mexido na plataforma antes, e com isso aprendi algumas coisas que podem ajudar principalmente os marinheiros de primeira viagem.

## Criar Projeto

### Adicionar watchOS target

Eu fiz a partir de um projeto iOS já existente - File->New->Target

![New Target](../assets/img/posts/SMCA/newWatchTarget.png)

e depois selecione a opção de WatchKit app (Se você for fazer um jogo, considere a opção do Game App - existe um SpriteKit adaptado para Apple Watch, com o quel eu não mexi, mas parece interessante).


![New Target](../assets/img/posts/SMCA/newWatchModel.png)

O se projeto de Apple Watch já estará criado e associado ao do iOS.

### Interface Builder (Storyboard)

Muito similar ao já de apps iOS (se você está familiarizado), mas com algumas diferenças.

### WatchKit

Outra diferença é que você não estará usando o **UIKit** do iOS, mas sim o **watchKit**, que no fundo no fundo são muito semelhantes, você acessa as propriedades dos seus objetos instanciados e modifica ainda como quiser. Mas por exemplie, não existe mais **TableViews** agora são apenas **Tables**.

Você ainda pode *ligar* os objetos do storyboard ao código usando @IBOutlets normalmente. Abaixo um exemplo tirado direto do SMCA:

![Storyboard WatchOS example](../assets/img/posts/SMCA/watchStoryboard.png)

O que você deve ter notado de diferente são as entradas de Static Interface e Dynamic Interface (o que são para que servem onde habitam?) - mas não se preocupe muito com elas.

O que acontece é que, apps iOS podem receber notificações, e caso tenham uma extensão para watchOS de notificações, essas também vão aparecer no seu Apple Watch.
Mas não se preocupe muito com isso, apenas deixa essas duas interfaces ali, você não precisa modificá-las caso não queira.

Outra coisa legal é o scroll automático, se você notar, o Interafce principal na imagem de cima é enorme - e não tem nenhum Watch desse tamanho todo. Mas o watchKit faz o scroll automático nesse tipo de tela, tanto pelo touch, quanto pelo uso da coroa do Apple Watch.

### Navegação

A navegação do app em watchOS tem uma particularidade interessante: **Ela só segue uma direção**. Como assim?

Existem duas formas de ligar os Interface Controllers: lateralmente, ou através de push. Mas nunca **as duas ao mesmo tempo** (pelo menos é o que diz a documentação oficial, e eu realmente aão consegui fazer as duas porque ele gera um erro assim que a mistura de navegações é detectada).

#### Navegação horizontal
Ligue as interfaces com a relação do tipo **Next Page**, isso já fará automaticamente que o seu app seja **paginado**, permitiando rolar entre suas páginas.

![Storyboard WatchOS example](../assets/img/posts/SMCA/nextPage.png)

### Navegação por push
Essa é a utilizada no SMCA, e a mais *comum* de iOS, quando coloca uma página por cima da outra, com o botão de voltar em cima.

Para isso, basta ter 2 Interface Builders diferentes, com identificadores distintos.
Você então adiciona uma ação que faz o seguinte:

```swift
@IBAction func gotToNextScreen(_ sender: Any) {
        self.pushController(withName: "nextScreen", context: nil)
}
```

Isso já gera a navegação, o botão de voltar, o título e tudo mais automaticamente, então configure tudo bonitinho.


> **Dica** - Usar mais de um storyboard é sucesso, mas diferente também. Dentre todos (TODOS) os seus Interface Builders, apenas um deve ser marcado como **Initial Controller**, mesmo se em Storyboards distintos.


### Gestos
Eu tive um pouco de dificuldade de instanciar os gestos programaticamente como estava acostumado no iOS:

```swift
let gesture = UIGestureRecognizer(...)
self.view.addGestureRecognizer(gesture)
```

ENTÃO, usei os gestos instanciados diretamente no storyboard (se você jogou o SMCA versão de Watch, vai ver que ele tem um jogo da memória de gestos, então mexi bastante com eles).

![Gesture watch storyboard](../assets/img/posts/SMCA/gesturesWatch.png)

Adicione os gestos onde você quer que eles atuem, e faça um Outlet do tipo **Action** para captar os gestos.

"*Ah, mas e se eu quiser configurar os gestos, como mudar duração, tamanho do swipe e tudo mais?*"

Faça um Outlet comum e altere essas propriedades quando a tela for instanciada.

```swift
@IBOutlet var down: WKSwipeGestureRecognizer!
@IBOutlet var left: WKSwipeGestureRecognizer!
@IBOutlet var right: WKSwipeGestureRecognizer!

override func didAppear() {
    super.didAppear()
    down.direction = .down
    left.direction = .left
    right.direction = .right
}

```

### Importantíssimo para Desenvolvimento em Swift
Se você está desenvolvendo em Swift, existe um bug na interface Xcode Watch que não aparece em testes do simulador. (Acredite, eu levei uns 5 dias até achar isso), onde o app não começa nunca. Ele carrega mas fica em **load infinito**.

Para resolver esse problema, você deve adicionar uma opção no modo de build do seu app.

Vá em **PROJECT**, para adicionar as configurações para o app todo e clique no **+** para adicionar uma nova opção.


![Add Option](../assets/img/posts/SMCA/addOption.png)

Digite na linha:
``` STRIP_BITCODE_FROM_COPIED_FILES ```
e coloque como ```NO```.

Isso deve resolver o problema.

> De novo, isso já era um erro conhecido para o qual encontrei solução nos fóruns da Apple.

## Dicas de Design

Acho que do lado de desenvolvimento uma coisa tem que ficar muito clara **Você está rodando em um relógio**, ou seja, sem muito processamento e **bateria** disponíveis.

Use animações moderadamente e desative-as sempre que possível - para não gastar processamento calculando as suas animações.

Prefira o background preto - quanto menor o uso de cores e luzes, menos bateria você gasta - mas faça um bom trabalho de branding para mão perder a indentidade do seu app.

Leve em conta qual o tipo de transição/navegação que será utilizada no seu app também.

---

Boa sorte desenvolvendo seu app para watch! Se esse tutorial te ajudou, conte pra mim depois 😉.

Se quiser jogar o **Super Mini Color Arcade**, para Watch ou iOS, baixe agora na app Store:

[![Super Mini Color Arcade Download Image](../assets/img/posts/SMCA/download.png)](https://itunes.apple.com/us/app/super-mini-color-arcade/id1375643857?mt=8)


###### Cover Image background <a href='https://www.freepik.com/free-vector/vintage-clocks-pattern_857152.htm'>designed by Freepik</a>
