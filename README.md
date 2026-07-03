# ElectronKT Bridge

ElectronKT Bridge é uma biblioteca para **Kotlin/JVM** desenvolvida para simplificar a criação de aplicações desktop utilizando tecnologias Web através do **ElectronKT**.

O objetivo da biblioteca é oferecer uma API simples e intuitiva para que aplicações Kotlin possam manipular interfaces HTML, CSS e JavaScript sem que o desenvolvedor precise implementar manualmente a comunicação entre o processo Kotlin e o Electron.

Toda a comunicação entre as duas partes é realizada automaticamente pela biblioteca através de um protocolo baseado em **STDIN**, **STDOUT** e mensagens **JSON**, permitindo uma integração bidirecional entre Kotlin e JavaScript.

## Principais recursos

* Criação de interfaces utilizando HTML, CSS e JavaScript.
* Manipulação dinâmica do DOM diretamente pelo Kotlin.
* Comunicação bidirecional entre Kotlin e JavaScript através de JSON.
* Execução de código JavaScript em tempo de execução.
* Aplicação dinâmica de estilos CSS.
* Captura de eventos da interface, como cliques, envio de formulários e atalhos de teclado.
* API simples, sem necessidade de configurar IPC, Preload Scripts ou Context Bridge manualmente.

## Como funciona

A arquitetura do ElectronKT Bridge é composta por duas partes:

* **ElectronBridge**, responsável pela comunicação do lado Kotlin.
* **ElectronKT**, responsável pela renderização da interface utilizando o Electron.

O fluxo de comunicação acontece automaticamente da seguinte forma:

```text
           Kotlin/JVM
                │
                ▼
        ElectronBridge
                │
        STDIN / STDOUT
                │
                ▼
           ElectronKT
                │
                ▼
      HTML • CSS • JavaScript
```

O desenvolvedor trabalha apenas com a API disponibilizada pela biblioteca, enquanto toda a comunicação entre os processos é abstraída pelo ElectronKT Bridge.

---

# API Kotlin

A biblioteca disponibiliza métodos para controlar completamente a interface da aplicação.

## Manipulação da interface

* `LoadHTML()` — Carrega uma página HTML completa.
* `LoadFileIndex()` — Carrega automaticamente o arquivo `index.html` da aplicação.
* `ATTBODY()` — Substitui apenas o conteúdo do elemento `<body>`.
* `ADDBODY()` — Adiciona novos elementos ao final do `<body>`.
* `LoadCSS()` — Aplica estilos CSS dinamicamente em qualquer elemento.
* `ADDJS()` — Injeta código JavaScript em tempo de execução.
* `ExecFunJS()` — Executa uma função JavaScript existente.
* `Alert()` — Exibe um alerta nativo da aplicação.

## Eventos

A biblioteca permite receber eventos gerados pela interface Web.

* `EventClick()` — Eventos de clique.
* `EventSubmit()` — Envio de formulários.
* `EventPressKey()` — Atalhos e teclas pressionadas.
* `EventMSG()` — Mensagens personalizadas enviadas pelo JavaScript.

## Comunicação

O método `SendMSG()` envia qualquer objeto JSON do Kotlin para o JavaScript.

Da mesma forma, a função JavaScript `send()` envia mensagens para o Kotlin, que podem ser tratadas através do evento `EventMSG()`.

Toda a comunicação utiliza JSON como formato de troca de dados.

---

# API JavaScript

Quando uma página é carregada pelo ElectronKT Bridge, duas funções ficam automaticamente disponíveis no contexto JavaScript.

## send()

Envia uma mensagem do JavaScript para o Kotlin.

As mensagens são recebidas através do evento `ElectronBridge.EventMSG()`.

## receive()

Recebe mensagens enviadas pelo Kotlin através do método `ElectronBridge.SendMSG()`.

Essa comunicação é totalmente transparente para o desenvolvedor, dispensando qualquer configuração manual de IPC ou Context Bridge.

---

# Detalhes
## Instalação

Adicione a biblioteca ao projeto:

```kotlin
dependencies {
    implementation(files("libs/ElectronKT-1.0.jar"))
}
```

## Inicialização

Toda comunicação com o Electron começa através da função `start`.

```kotlin
ElectronBridge.start(
    pastaElectron = File("ElectronKT"),
    tela = "1200x700",
    nomeApp = "Minha Aplicação",
    iconeNome = "icon.png"
)
```

### Parâmetros

| Parâmetro       | Descrição                                   |
| --------------- | ------------------------------------------- |
| `pastaElectron` | Caminho da instalação do ElectronKT.        |
| `tela`          | Resolução inicial da janela.                |
| `nomeApp`       | Nome exibido pela aplicação.                |
| `iconeNome`     | Nome do ícone localizado na pasta `public`. |

---

# Manipulação da Interface

## LoadHTML()

Substitui completamente o conteúdo da página.

```kotlin
ElectronBridge.LoadHTML("""
<h1>Hello World</h1>
""")
```

---

## ADDBODY()

Adiciona conteúdo ao `<body>` existente.

```kotlin
ElectronBridge.ADDBODY("""
<div>Nova seção</div>
""")
```

---

## ATTBODY()

Substitui apenas o conteúdo do `<body>`.

```kotlin
ElectronBridge.ATTBODY("""
<div>Novo conteúdo</div>
""")
```

---

## LoadCSS()

Aplica estilos CSS dinamicamente. Pode ser uma classe, um id ou o nome de um elemento como div.

```kotlin
ElectronBridge.LoadCSS(
    ".card",
    "background:#2196F3;color:white;"
)
```

---

## ADDJS()

Adiciona um novo script JavaScript.

```kotlin
ElectronBridge.ADDJS("""
console.log("Hello");
""")
```

---

## ExecFunJS()

Executa uma função JavaScript já existente.

```kotlin
ElectronBridge.ExecFunJS("minhaFuncao()")
```

---

## Alert()

Exibe um alerta nativo.

```kotlin
ElectronBridge.Alert("Operação realizada.")
```

---

# Eventos

## EventClick()

Recebe eventos de clique enviados pelo Electron.

```kotlin
ElectronBridge.EventClick {

    val json = JSONObject(it)

    println(json.getString("id"))

}
```

---

## EventPressKey()

Recebe eventos de teclado.

```kotlin
ElectronBridge.EventPressKey {

    println(it)

}
```

---

## EventSubmit()

Recebe eventos de formulários.

```kotlin
ElectronBridge.EventSubmit {

    println(it)

}
```

---

## EventMSG()

Recebe mensagens personalizadas enviadas pelo Electron.

```kotlin
ElectronBridge.EventMSG {

    println(it)

}
```

---

# Comunicação

## SendMSG()

Envia um objeto JSON para o Electron.

```kotlin
ElectronBridge.SendMSG("""
{
    "usuario":"Bruno",
    "nivel":2
}
""")
```

---

# Arquivos HTML

## LoadFileIndex()

Carrega automaticamente o arquivo `index.html` da aplicação Electron.

```kotlin
ElectronBridge.LoadFileIndex()
```

---


# Requisitos

* JDK 25 ou superior.
* Kotlin/JVM.
* ElectronKT instalado junto ao projeto.
* Linux (Ubuntu e derivados).

Atualmente o ElectronKT Bridge possui suporte oficial apenas para Linux. O objetivo do projeto é disponibilizar versões compatíveis com Windows e macOS futuramente.

---

# Objetivo do projeto

O ElectronKT Bridge nasceu com o propósito de oferecer ao ecossistema Kotlin uma alternativa simples para desenvolvimento de interfaces desktop modernas utilizando tecnologias Web.

O foco da biblioteca é permitir que o desenvolvedor escreva a lógica da aplicação em Kotlin enquanto utiliza HTML, CSS e JavaScript para construir interfaces ricas, mantendo uma comunicação rápida, transparente e totalmente integrada entre os dois ambientes.




#Download do ElectronKT

O **ElectronKT Bridge** depende do **ElectronKT**, que é o motor responsável pela renderização da interface gráfica.

Por questões de distribuição, o ElectronKT **não acompanha** o arquivo `.jar` da biblioteca e deve ser baixado separadamente.

> **Download da versão mais recente:**
> **[📦 Baixar ElectronKT](https://drive.google.com/file/d/1OCs05nBLGLIQ_FlMEHlt0BLY_wnC_t3y/view?usp=sharing)**

Após o download, extraia a pasta **ElectronKT** na raiz do seu projeto. A estrutura deverá ficar semelhante à seguinte:

```text
MeuProjeto/
├── ElectronKT/
│   ├── electronkt
│   ├── resources/
│   ├── public/
│   └── ...
├── libs/
│   └── ElectronKT-1.0.jar
├── src/
└── build.gradle.kts
```

Na inicialização da aplicação, informe o caminho da pasta através do parâmetro `pastaElectron`:

```kotlin
ElectronBridge.start(
    pastaElectron = File("ElectronKT"),
    tela = "1200x700",
    nomeApp = "Minha Aplicação",
    iconeNome = "icon.png"
)
```

> **Importante:** O parâmetro `pastaElectron` deve apontar para a pasta onde o ElectronKT foi extraído.


