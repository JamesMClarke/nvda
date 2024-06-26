# ﻿Guia do Desenvolvedor do NVDA NVDA_VERSION

[TOC]

Nota; Alguns termos deverão ser mantidos em Inglês por referirem-se a programação e Jargões Técnicos



## Introdução {#toc2}

Esse manual proporciona informações concernentes ao desenvolvimento do NVDA, incluindo tradução e o desenvolvimento de componentes para este programa.

### Uma Nota Sobre Python {#toc3}

O NVDA e seus componentes são escritos primariamente na linguagem de programação Python.
Não é o objetivo deste manual ensiná-lo sobre python, ainda que exemplos sejam fornecidos ao longo deste documento para ajudá-lo a familiarizar-se com a sintaxe desta linguagen.
Documentação e outros recursos relacionados com python podem ser encontrados no cite [[www.python.org/](http://www.python.org/)].

## Tradução {#toc4}

Para suportar muitos idiomas/localizações, o NVDA deve ser traduzido e devem ser fornecidos dados específicos para a localização.

### Descrições de Caracteres {#toc5}

Algumas vezes, é muito difícil ou mesmo impossível distinguir um caractere de outro.
Por exemplo, dois caracteres podem ser pronunciados da mesma forma, embora sejam caracteres diferentes.
Para ajudar aos usuários quando isso ocorre, podem ser fornecidas descrições de caracteres que descrevem o caractere de uma única forma.

Descrições de caracteres podem ser fornecidas para uma localização em um um arquivo denominado characterDescriptions.dic na pasta desta localização.
Este é um arquivo de texto codificado em UTF-8.
Linhas em branco ou iniciadas por cardinal "#" são ignoradas.
Todas as outras linhas devem conter um caractere seguido de um tab, e em seguida uma ou mais descrições separadas por tab.

Por exemplo:

    # Esse é um Comentário
    a	alfa
    b	bravo

Veja o arquivo locale\pt_BR\characterDescriptions.dic para um exemplo completo. 

### Pronúncia de Sinais {#toc6}

É bastante útil ouvir a pontuação e outros sinais pronunciados como palavras quando lemos textos, particularmente ao movermo-nos por caracteres.
Infelizmente, a pronúncia de sinais é inconsistente entre sintetizadores de voz e muitos sintetizadores não falam vários sinais e/ou não permitem controlar que sinais serão falados.
Por isso, o NVDA oferece informações sobre pronúncia de sinais a ser fornecida.

Isso é feito para uma localização, fornecendo um arquivo denominado symbols.dic em sua pasta.
Esse é um arquivo de texto codificado em UTF-8.
Linhas em branco ou iniciadas por cardinal "#" são ignoradas.
Todas as localizações inerentemente herdam a informação do sinal para Inglês, embora qualquer dessas informações possam ser substituidas. 

O arquivo contém duas seções.

#### Definindo Sinais Complexos {#toc7}

A primeira seção é opcional e define expressões regulares originais para sinais complexos.
Sinais complexos são sinais que não são simplesmente um caractere ou seqüência de caracteres, mas em lugar disso necessitam de uma combinação mais complicada.
Um exemplo disso é a parada completa (.) fim de sentença em português.
O ponto "." é usado para muitos propósitos, então uma checagem mais complicada é necessária para determinar se esse se refere ao fim de uma sentença.

A seção de sinais complexos inicia-se com a linha:

    complexSymbols:

As linhas seguintes contêm um identificador textual usado para identificar o sinal, um tab e a expressão regular original para aquele sinal.
Por exemplo:

    . sentence ending	(?<=[^\s.])\.(?=[\"')\s]|$)

Repetindo, os sinais em inglês são herdados por todas as outras localizações, de modo que você não precisa incluir qualquer sinal complexo já definido para Inglês.

#### Definindo Informações de Sinais {#toc8}

A segunda seção proporciona informações sobre quando e como pronunciar sinais.
Ela inicia-se com a linha:

    symbols:

As linhas seguintes devem conter vários campos separados por tab.
Os únicos campos obrigatórios  são o do identificador e substituto.
O padrão será usado para campos omitidos.
Os campos são os seguintes:

* identificador: O identificador do sinal.
Na maior parte dos casos, é apenas o caractere ou caracteres do sinal.
Todavia, ele pode também ser o identificador de um sinal complexo.
Certos caracteres não podem ser digitados dentro do arquivo, então as seguintes  seqüências especiais podem ser usadas:
* \0: Null
* \t: Tab
* \n: fim de linha
* \r: Retorno
* \f: form feed
* \#: # caractere (necessário porque cardinal # no início de uma linha denota um comentário)
* substituto: O texto que deve ser falado para o sinal.
* grau: O grau de sinais no qual o sinal deve ser falado.
O grau de sinais é configurado pelo usuário e especifica a quantidade de sinais que deve ser falada.
Esse campo deve conter um dos graus "nada", "pouco", "muito", "tudo" ou "caractere", ou "-" usar o padrão.
"caractere" significa que o sinal só deve ser pronunciado ao mover-se por caractere.
O padrão é para herdar o valor ou "all" se nada existe para herdar.
* preservar: Se o próprio sinal deve ser preservado para facilitar a pronúncia correta pelo sintetizador.
Por exemplo, sinais que provocam pausas ou inflecção (tais como a vírgula em português) devem ser preservados.
Esse campo deve ser um dos seguintes:
* never: Nunca preservar o sinal.
* always: Sempre preservar o sinal.
* norep: Preservar o sinal apenas se ele não estiver sendo substituido; isto é, o usuário  tiver ajustado o grau de sinais para um menor que o grau deste.
* -: Usar o padrão.
  O padrão é para herdar o valor ou "never" se nada existe para herdar.

Finalmente, um nome para exibição para o sinal pode ser fornecido num comentário depois de um tab ao final da linha.
Esse será mostrado aos usuários ao editar a informação do sinal e é útil especialmente para os tradutores definirem nomes traduzidos para sinais complexos oriúndos do inglês.

Aqui vão alguns exemplos:

    (	abre parêntese	most

Isso significa que o caractere "(" deve ser falado como "abre parêntese" apenas quando o grau de sinais estiver ajustado para muito ou superior; isto é, muito ou tudo.

    ,	vírgula	all	always

Isso significa que o caractere "," deve ser falado como "vírgula" quando o grau de sinais estiver ajustado para "tudo" e que o próprio caractere sempre deve ser preservado de modo que o sintetizador efetue a pausa apropriadamente.

    . sentence ending	point	# . fin de phrase

Essa linha aparece no arquivo symbols.dic em francês.
Isso significa que ". finais de sentença" sinais complexos devem ser falados como "point".
O grau e o modo de preservação não são especificados, pois serão tomados do Inglês.
Um nome para amostra é fornecido de modo que os usuários franceses conhecerão o que o sinal representa.

Por favor consulte o arquivo locale\pt_Br\symbols.dic para as definições que são herdadas do Inglês para todas as localizações.
Esse também é um bom exemplo completo.

## Plugins {#toc9}
### Resumo {#toc10}

Plugins lhe permitem personalizar a forma de o NVDA comportar-se em geral ou dentro de um aplicativo em particular.
Eles são capazes de:

* Responder a eventos em particular como mudanças no foco ou em propriedades dos objetos; isto é, quando um controle altera seu nome.
* Implementar comandos que estão vinculados ao pressionamento de teclas em particular ou outras entradas.
* Personalizar o comportamento  e implementar funcionalidades adicionais para controles em particular.
* Personalizar ou adicionar novo suporte para conteúdo de texto e documentos complexos.

Essa seção proporciona uma introdução para o desenvolvimento de plugins.
Os desenvolvedores devem consultar a documentação do código  para uma referência completa.

### Tipos de Plugins {#toc11}

Existem dois tipos de plugins. eles são:

* App Modules: códigos específicos para um aplicativo em particular.
Um App Module recebe todos os eventos para um aplicativo em particular, mesmo qe este não esteja atualmente ativo.
Quando o aplicativo está ativo, qualquer comando a que o App Module esteja vinculado pelo pressionamento de teclas ou outras entradas pode ser executado pelo usuário.
* Global Plugins: Códigos globais para o NVDA, ou seja, é usado em todos os aplicativos.
Global Plugins recebem todos os eventos para todos os controles no sistema operacional
Quaisquer comandos vinculados a Global Plugins podem ser executados pelo usuário onde ele estiver no sistema operacional, indiferente do aplicativo.

Se você quizer melhorar o acesso do NVDA para um aplicativo em particular, é muito provável que você pretenderá escrever um App Module.
Contrariamente, caso queira adicionar alguma funcionalidade ao NVDA em geral (tal como um script para anunciar a velocidade da conecção de internet atual estando em qualquer aplicativo), então um Global Plugin éo que você deseja.

Tanto App Modules como Global Plugins compartilham um aspecto comum.
Ambos são arquivos em código Python (com a extensão .py), ambos definem uma classe especial contendo todos os eventos, scripts e vínculos, e  podem definir classes personalizadas para acessar controles, conteúdos de texto e documentos complexos.
Todavia, eles diferenciam-se de algumas formas.

As seguintes  seções falarão separadamente sobre App Modules e Global Plugins.
Depois deste ponto, a discussão voltará a ser geral.

### Básicas sobre um App Module {#toc12}

Arquivos de App Modules têm a extensão .py, e tem o mesmo nome do principal executável do aplicativo para o qual você desejará que seja usado.
Por exemplo, um App Module para o notepad deverá ser denominado notepad.py, visto que o principal executável do notepad chama-se notepad.exe.

Arquivos de App Modules devem ser armazenados na sub-pasta appModules da pasta de configurações do usuÁrio do NVDA.
Para mais informações sobre onde encontrar a pasta de configurações do usuário, por favor consulte o Guia do Usuário do NVDA.

Os App Modules devem definir uma classe denominada AppModule, que herda do appModuleHandler.AppModule.
Essa classe pode então definir eventos e métodos de scripts, gestos de vínculos e outros códigos.
Isso tudo será explicado profundamente mais tarde.

O NVDA carrega um App Module para um aplicativo logo que identifica que o mesmo está sendo executado.
O App Module é descarregado logo que o aplicativo é fechado ou quando o NVDA é encerrado. 

### Exemplo 1: Um App Module para Bipar em Eventos de Mudanças no Foco {#Example1}

O exemplo seguinte de App Module faz com que o NVDA reproduza um beep cada vez que o foco muda dentro do aplicativo notepad.
Esse exemplo lhe mostra o esquema básico de um App Module.

Copie e cole o código (mas sem incluir) os marcadores inicial e final para um novo arquivo de texto denominado notepad.py, que deve ser salvo na sub-pasta AppModules.
Seja bastante cuidadoso  para manter todos os tabs e espaços intactos.

Uma vez salvo no local correto, reinicie o NVDA ou selecione Recarregar Plugins localizado em Ferramentas no menu do NVDA.

Finalmente, abra o Notepad e mova o foco pelo aplicativo; isto é, mova-o ao longo da barra de menus, abra alguma caixa de diálogo, etc.
Você deve ouvir beeps a cada vez que o foco mudar.
Note que se você se mover para uma instância fora do Notepad, para o Windows Explorer - você não ouvirá beeps.

    --- start ---
    # Notepad App Module for NVDA
    # Developer guide example 1
    
    import appModuleHandler
    
    class AppModule(appModuleHandler.AppModule):
    
    	def event_gainFocus(self, obj, nextHandler):
    		import tones
    		tones.beep(550, 50)
    		nextHandler()
    
    --- end ---

Esse arquivo de App Module inicia-se com duas linhas de comentário, que descrevem para que o arquivo serve.

Ele então importa o módulo appModuleHandler, de modo que o o App Module então possa ter acesso à classe base do AppModule.

Em seguida, ele define uma classe denominada AppModule, que é herdada do appModuleHandler.AppModule.

Dentro dessa classe, ele define 1 ou mais eventos, scripts ou gestos vinculados.
Nesse exemplo, ele define 1 método de evento para gainFocus events (event_gainFocus), que reproduz um pequeno beep cada vez que é executado.
A implementação desse evento não é importante para o propósito desse exemplo.
A parte mais important é a própria classe.
Eventos serão explicados com mais detalhes noutro momento.

Como com outros exemplos nesse manual, lembramos que ao apagar o app module criado ao terminar o teste depois reiniciar o NVDA ou recarregar os plugins, então a funcionalidade original será restaurada.

### Básicos sobre Global Plugins {#toc14}

Arquivos de Global Plugins tem a extensão .py, e devem ter um único e curto nome que identifica  o que ele faz.

Arquivos de Global Plugins devem ser armazenados na sub-pasta globalPlugins da pasta de configurações do usuário do NVDA.
Para mais informações sobre onde encontrar a pasta de configurações do usuário, por favor consulte o Guia do Usuário do NVDA.

Global Plugins devem definir uma classe denominada GlobalPlugin, que herda do globalPluginHandler.GlobalPlugin.
Essa classe pode então definir eventos e métodos de scripts, gestos vinculados e outros códigos.
Isso tudo será explicado de forma mais profunda posteriormente.

O NVDA carrega todos os global plugins logo que é iniciado, e os descarrega ao encerrar. 

### Exemplo 2: Um Global Plugin que Proporciona um Script Para Anunciar a Versão do NVDA {#toc15}

O exemplo seguinte de Global Plugin lhe permite pressionar NVDA+shift+v em qualquer lugar do sistema operacional para saber a informação da  versão do NVDA. 
Esse exemplo serve apenas para lhe mostrar o esquema básico de um Global Plugin.

Copie e cole o código (sem incluir) os marcadores de início e fim para um novo arquivo de texto com o nome de exemplo2.py, que deve ser salvo na sub-pasta globalPlugins.
Seja bastante cuidadoso para manter todos os tabs e espaços intactos.

Uma vez salvo no lugar correto, então reinicie o NVDA ou selecione Recarregar Plugins localizado em ferramentas no menu do NVDA.

DE qualquer parte, você agora poderá pressionar NVDA+shift+v para ter a versão do NVDA falado e exibido em braille.

    --- start ---
    # Version announcement plugin for NVDA
    # Developer guide example 2
    
    import globalPluginHandler
    import ui
    import versionInfo
    
    class GlobalPlugin(globalPluginHandler.GlobalPlugin):
    
    	def script_announceNVDAVersion(self, gesture):
    		ui.message(versionInfo.version)
    
    	__gestures={
    		"kb:NVDA+shift+v": "announceNVDAVersion",
    	}
    
    --- end ---

Esse arquivo de Global Plugin inicia-se com duas linhas de comentário, que descrevem para que serve o arquivo.

Ele então importa o módulo globalPluginHandler, de modo que o Global Plugin tenha acesso à classe base do GlobalPlugin.

Ele também importa alguns outros módulos, nomeadamente ui e versionInfo, que esse plugin específico precisa para realizar as ações necessárias para  anunciar a versão.

Em seguida, ele define uma classe denominada GlobalPlugin, que é herdada do globalPluginHandler.GlobalPlugin.

Dentro dessa classe, ele define 1 ou mais eventos, scripts ou gestos vinculados.
Nesse exemplo, ele define um método de script que proporciona o anúncio da version, e fornece um vínculo a partir de NVDA+shift+v para esse script.
Todavia, os detalhes do script e seus vínculos não são importantes para o propósito desse exemplo.
A parte mais importante é a própria classe.

Como com outros exemplos nesse manual, lembramos que ao apagar o Global Plugin criado ao terminar o teste depois reiniciar o NVDA ou recarregar os plugins, então a funcionalidade original será restaurada.

### Objetos no NVDA {#toc16}

O NVDA representa controles e outros GUI elementos como seus Objetos.
Esses Objetos do NVDA contêm propriedades standardisadas, tais como nome, função, valor, estados e descrição, que permitem a outras partes do NVDA consultar ou apresentar informações sobre um controle de uma forma generalizada.
Por exemplo, o botão OK num diálogo pode ser representado como um Objeto do NVDA com o nome de "OK" e a função de botão.
Semelhantemente, uma caixa de seleção com a etiqueta de "Eu Concordo" pode ter o nome de "Eu concordo", a função de caixa de seleção, e se atualmente estiver marcada, o estado de marcada.

As there are many differents GUI Toolkits and platform and accessibility APIs, NVDA Objects abstract these differences into a standard form that NVDA can use, regardless of the toolkit or API a particular control is made with.
For example, the Ok button, just discussed could be a widget in a Java application, an MSAA object, an IAccessible2 object or a UI Automation element. 

Os Objetos no NVDA  possuem muitas propriedades.
Algumas das  mais úteis são:

* name: a etiqueta do controle.
* role: one of the ROLE_* constants from NVDA's controlTypes module.
Botão, diálogo, texto editável, janela e caixa de seleção são exemplos de funções.
* states: a set of 0 or more of the STATE_* constants from NVDA's controlTypes module.
Enfocável, enfocado, selecionado, selecionável, expandido, recolhido e marcado são alguns exemplos de estados.
* value: o valor do controle; isto é, a porcentagem de uma barra de deslocamento ou a configuração atual de uma caixa de combinação.
* description: uma sentença ou duas descrevendo o que o controle faz (normalmente, o mesmo que sua dica de ferramenta).
* location: the object's left, top, width and height positions in screen coordinates.  
* parent: this object's parent object.
For example, a list item object's parent would be the list containing it.
* next: the object directly after this one on the same level in logical order.
For example, a menu item NVDA Object's next object is most likely another menu item within the same menu.
* previous: like next but in reverse.
* firstChild: the first direct child object of this object.
For example, a list's first child would be the first list item.
* lastChild: the last direct child of this object.
  * children: a list of all the direct children of this object; e.g. all the menu items in a menu.
-

There are also a few simplified navigation properties such as simpleParent, simpleNext, simpleFirstChild and simpleLastChild.
These are like their respective navigation properties described above, but NVDA filters out unuseful objects.
These properties are used when NVDA's simple review mode is turned on, which is the default.
These simple properties may be easier to use, but the real navigation properties more closely reflect the underlying Operating System structure.

When developing plugins, most of the time, it is not important what toolkit or API backs an NVDA Object, as the plugin will usually only access standard properties such as name, role and value.
However, as plugins become more advanced, it is certainly possible to delve deeper into NVDA Objects to find out toolkit or API specific information if required.

Plugins make use of NVDA Objects in three particular ways:
* Most events that plugins receive take an argument which is the NVDA Object on which the event occurred.
For example, event_gainFocus takes the NVDA Object that represents the control gaining focus.
* Scripts, events or other code may fetch objects of interest such as the NVDA Object with focus, NVDA's current navigator object, or perhaps the Desktop NVDA Object.
The code may then retreave information from that object or perhaps even retreave another object related to it (e.g. its parent, first child, etc.).
* the Plugin may define its own custom NVDA Object classes which will be used to wrap a specific control to give it extra functionality, mutate its properties, etc.

Just like App Modules and Global Plugins, NVDA Objects can also define events, scripts and gesture bindings. 

### Scripts e Gestos Vinculados {#toc17}

App Modules, Global Plugins e Objetos no NVDA podem definir métodos especiais que podem ser vinculados a uma parte de uma entrada tal como o pressionamento de uma tecla.
O NVDA identifica esses métodos como scripts.

A script is a standard Python instance method with a name starting with "script_"; e.g. "script_sayDateTime".

A script method takes two arguments:

* self: a reference to the App Module, Global Plugin or NVDA Object instance the script was called on.
* gesture: an Input Gesture object, which represents the input that caused the script to run.

As well as the actual script method, some form of gesture binding must be defined, so that NVDA knows what input should execute the script.

To bind a gesture to a script, a special "__gestures" Python dictionary can be defined as a class variable on the App Module, Global Plugin or NVDA Object.
These dictionaries should contain gesture identifier strings pointing to the name of the requested script, without the "script_" prefix.

There are more advanced ways of binding gestures in a more dynamic fashion, though the __gestures dictionary is the simplest.

A gesture identifier string is a simple string representation of a piece of input.
It consists of a two leter character code denoting the source of the input, an optional device in brackets, a colon (:) and one or more names separated by a plus (+) denoting the actual keys or input values.

Alguns exemplos de seqüências de identificadores de gesto são:

* "kb:NVDA+shift+v"
* "br(freedomScientific):leftWizWheelUp"
* "kb(laptop):NVDA+t"

Atualmente, os únicos códigos de entrada no NVDA são:

* kb: entrada de teclado
* br: entrada de braille

When NVDA receives input, it looks for a matching gesture binding in a particular order.
Once a gesture binding is found, the script is executed and no further bindings are used, nore is that particular gesture passed on automatically to the Operating System.

The order for gesture binding lookup is:

* Loaded Global Plugins
* App Module of the active application
* Tree Interceptor of the NVDA Object with focus if any; e.g. a virtualBuffer
* NVDA Object with focus
* Global Commands (built in commands like quitting NVDA, object navigation commands, etc.)

### Exemplo 3: Um Global Plugin para Obter  a Classe da Janela e a Identificação do Controle {#toc18}

O Global Plugin a seguir lhe permite pressionar NVDA+seta a esquerda para obter o anúncio da classe da janela atualmente em foco, e NVDA+seta a direita para obter o anúncio da identificação do controle atualmente em foco.
Esse exemplo lhe mostra como definir um ou mais scripts e gestos vinculados numa classe tal como um App Module, Global Plugin o objeto do NVDA.

Copie e cole o código (sem incluir) os marcadores de início e fim para um novo arquivo de texto com o nome de exemplo3.py, que deve ser salvo na sub-pasta globalPlugins.
Seja bastante cuidadoso para manter todos os tabs e espaços intactos.

Uma vez salvo no lugar correto, então reinicie o NVDA ou selecione Recarregar Plugins localizado em ferramentas no menu do NVDA.

    --- start ---
    #Window utility scripts for NVDA
    #Developer guide example 3
    
    import globalPluginHandler
    import ui
    import api
    
    class GlobalPlugin(globalPluginHandler.GlobalPlugin):
    
    	def script_announceWindowClassName(self, gesture):
    		focusObj = api.getFocusObject()
    		name = focusObj.name
    		windowClassName = focusObj.windowClassName
    		ui.message("class for %s window: %s" % (name, windowClassName))
    
    	def script_announceWindowControlID(self, gesture):
    		focusObj = api.getFocusObject()
    		name = focusObj.name
    		windowControlID = focusObj.windowControlID
    		ui.message("Control ID for %s window: %d" % (name, windowControlID))
    
    	__gestures = {
    		"kb:NVDA+leftArrow": "announceWindowClassName",
    		"kb:NVDA+rightArrow": "announceWindowControlID",
    	}
    
    --- end ---
### Eventos {#toc19}

When NVDA detects particular toolkit, API or Operating System events, it abstracts these and fires its own internal events on plugins and NVDA Objects.

Although most events are related to a specific NVDA Object (e.g. name change, gain focus, state change, etc.), these events can be handled at various levels. 
When an event is handled, it is stopped from going further down the chain.
However, code inside the event can choose to propagate it further if needed.

The order of levels through which the event passes until an event method is found is:

* Loaded Global Plugins
* The App Module associated with the NVDA Object on which the event was fired
* The Tree Interceptor (if any) associated with the NVDAObject on which the event was fired
* the NVDAObject itself.

Events are Python instance methods, with a name starting with "event_" followed by the actual name of the event (e.g. gainFocus).

These event methods take slightly different arguments depending at what level they are defined.

If an event for an NVDA Object is defined on an NVDA Object itself, the method only takes one mandatory argument which is the 'self' argument; i.e. the NVDA Object instance).
Some events may take extra arguments, though this is quite rare.

If an event for an NVDA Object is defined on a Global Plugin, App Module or Tree Interceptor, the event takes the following arguments:

* self: the instance of the Global Plugin, App Module or Tree Interceptor
* obj: the NVDA Object on which the event was fired
* nextHandler: a function that when called will propagate the event further down the chain.

Some common NVDA Object events are:

* foreground: this NVDA Object has become the new foreground object; i.e. active top-level object
* gainFocus
* loseFocus
* nameChange
* valueChange
* stateChange
* caret: when the caret (insertion point) within this NVDA Object moves
* locationChange: physical screen location changes

Existem muitos outros eventos, mas os listados acima são habitualmente os mais úteis.

Para um exemplo de um evento controlado por um App Module, por favor reveja o [exemplo 1](#Example1) (beeps para o foco no notepad).

### A variável SleepMode dos App Modules {#toc20}

Os App Modules possuem uma propriedade bastante útil denominada "sleepMode" (modo dormir), que se ajustada para "true" desabilitam quase que por completo o NVDA dentro daquele aplicativo.
O Modo Dormir é muito útil para aplicativos com voz própria que tenham funcionalidade de leitura de tela, ou mesmo possíveis jogos que necesssitam do uso do teclado completo.

Ainda que o Modo Dormir possa ser alternado entre ligado e desligado pelo usuário com o comando de teclado NVDA+shift+s, um desenvolvedor pode escolher tê-lo ativado por padrão para um determinado aplicativo.
Isso é feito fornecendo-se um App Module para esse aplicativo que simplesmente ajusta sleepMode para True na classe AppModule.

### Exemplo 4: Um App Module para ativar Modo Dormir {#toc21}

O código a seguir pode ser copiado e colado num arquivo de texto, depois salvo na pasta appModules com o nome do aplicativo para o qual você desejar ativar o modo dormir.
Como sempre, o arquivo deve ter a extensão .py.

    --- start ---
    import appModuleHandler
    
    class AppModule(appModuleHandler.AppModule):
    
    	sleepMode = True
    
    --- end ---
### Proporcionando Classes personalizadas para Objetos do NVDA {#toc22}

Providing custom NVDA Object classes is probably the most powerful and useful way to improve the experience of an application in an NVDA plugin.
This method allows you to place all the needed logic for a particular control altogether in one NVDA Object class for that control, rather than scattering code for many controls across a plugin's events.

There are two steps to providing a custom NVDA Object class:

* Define the NVDA Object class and its events, scripts, gesture bindings and overridden properties.
* Tell NVDA to use this NVDA Object class in specific situations by handling it in the plugin's chooseNVDAObjectOverlayClasses method.

When defining a custom NVDAObject class, you have many NVDAObject base classes to choose from.
These base classes contain the base support for the particular accessibility or OS API underlying the control, such as win32, MSAA or Java access Bridge.
You should usually inherit your custom NVDAObject class from the highest base class you need in order to choose your class in the first place.
For example, if you choose to use your custom NVDAObject class when the window class name is "Edit" and the window control ID is 15, you should probably inherit from NVDAObjects.window.Window, as clearly you are aware that this is a Window object.
Similarly, if you match on MSAA's accRole property, you would probably need to inherit from NVDAObjects.IAccessible.IAccessible.
You should also consider what properties you are going to override on the custom NVDA Object.
For instance, if you are going to override an IAccessible specific property, such as shouldAllowIAccessibleFocusEvent, then you need to inherit from NVDAObjects.IAccessible.IAccessible.

the chooseNVDAObjectOverlayClasses method can be implemented on app modules or global plugin classes.
It takes 3 arguments:

1. self: the app module or global plugin instance.
1. obj: the NVDAObject for which classes are being chosen.
1. clsList: a Python list of NVDAObject classes that will be used for obj.

Inside this method, you should decide which custom NVDA Object class(es) (if any) this NVDA Object should use by checking its properties, etc.
If a custom class should be used, it must be inserted into the class list, usually at the beginning. 
You can also remove classes chosen by NVDA from the class list, although this is rarely required.

### Exemplo 5: Etiquetando os Campos de Edição do Notepad usando a Personalização de Objeto do NVDA {#toc23}

Esse app module para o notepad faz com que o NVDA anuncie os principais campos de edição do Notepad como tendo um nome de "conteúdo".
Feito isto, quando este receber foco, o NVDA dirá "Edição de Conteúdo".

O código a seguir pode ser copiado e colado em um arquivo de texto, depois salvo na pasta appModules  com o nome de notepad.py.

    --- start ---
    import appModuleHandler
    from NVDAObjects.window import Window
    
    class AppModule(appModuleHandler.AppModule):
    
    	def chooseNVDAObjectOverlayClasses(self, obj, clsList):
    		if isinstance(obj, Window) and obj.windowClassName == "Edit" and obj.windowControlID == 15:
    			clsList.insert(0, LabelledEditField)
    
    class LabelledEditField(Window):
    
    	name="Content"
    
    --- end ---
## O Console Python do NVDA {#toc24}

 O console pyhton do NVDA emula o interpretador Python interativo de dentro do NVDA.
Essa é uma ferramenta de desenvolvimento que é muito útil para depuração, inspeção geral interna do NVDA ou inspeção da hierarquia de acessibilidade de um aplicativo.

### uso {#toc25}

O console pode ser ativado de duas formas:

* Pressionando NVDA+control+z.
Quando ativado dessa forma, um snapshot do atual estado do NVDA referente ao momento em que a tecla foi  presionada será tomado e salvo em certas variáveis disponíveis no console.
Consulte [ Variáveis Snapshot](#PythonConsoleSnapshotVariables) para mais detalhes.
* Selecionando Ferramentas -> Console Python a partir do menu NVDA na barra do sistema.

O console é semelhante ao interpretador Python standard interativo.
A entrada é aceita de uma linha por vez.
A linha atual é processada quando enter é pressionado.
Você pode navegar pelo históryco das linhas introduzidas anteriormente usando as setas para cima e para baixo.

Saídas (respostas vindas do interpretador) são anunciadas quando enter é pressionado.
A tecla f6 alterna entre os controles de entrada e saída.

Encerrando a janela do console simplesmente o oculta.
Isso permite que o usuário retorne à seção como foi deixada ao ser encerrada, incluindo o histórico e as variáveis.

### Namespace {#toc26}
#### Importações Automáticas {#toc27}

Por conveniência, os seguintes módulos e variáveis são importados automaticamente no console:
sys, os, wx, log (from logHandler), api, queueHandler, speech, braille

#### Snapshot Variables {#PythonConsoleSnapshotVariables}

Sempre que o comando NVDA+control+z é pressionado, certas variáveis disponíveis no console são atribuidas de acordo com o estado atual do NVDA.
Essas variáveis são:

* focus: O objeto atualmente em foco
* focusAnc: O objeto ascendente ao atualmente em  foco
* fdl: Diferença de Níveis no foco ; isto é, o nível ao qual o ascendente do  atual e o atual objeto em foco diferenciam-se
* fg: O objeto atualmente em primeiro plano
* nav: O objeto de navegação atual
* mouse: O objeto atualmente sob o mouse
* brlRegions: As regiões de braille do buffer braille ativo

