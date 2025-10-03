# CXPergunte - Função Personalizada para Parâmetros

## 📋 Descrição

A função **CXPergunte** é uma versão aprimorada da função padrão `Pergunte()` do Protheus/TOTVS, oferecendo maior flexibilidade na criação de telas de parâmetros. Permite definir perguntas dinamicamente através de arrays, sem necessidade de criar registros no SX1.

## 🚀 Principais Características

- ✅ **Compatibilidade Total**: Usa exatamente os mesmos parâmetros da função `Pergunte()` padrão
- ✅ **Perguntas Dinâmicas**: Define perguntas via array, sem depender do SX1
- ✅ **Multilíngue**: Suporte para Português, Espanhol e Inglês
- ✅ **Seleção de Arquivos Flexível**: Permite máscaras, diretório inicial e configurações avançadas
- ✅ **Help Personalizado**: Suporte a help customizado ou padrão do sistema
- ✅ **Vinculação de Variáveis**: Liga automaticamente MV_PAR## com variáveis personalizadas
- ✅ **Validações Customizadas**: Suporte a validações por código ou CodeBlock
- ✅ **Tipos Especiais**: Range, Combo, File e campos tradicionais

## 📝 Sintaxe

```advpl
User Function CXPergunte(
                        , ucPergunta     ; //-- Grupo de perguntas (obrigatório)
                        , ulAsk          ; //-- Exibir tela (.T./.F.)
                        , ucTitle        ; //-- Título da tela
                        , lOnlyView      ; //-- Legado - não usado
                        , oDlg           ; //-- Legado - não usado
                        , lUseProf       ; //-- Usar perfil de usuário (.T.)
                        , aPergs         ; //-- Array com definição das perguntas
                        , lBreakLine     ; //-- Quebra linha no título (.F.)
                        , lHasHelp       ; //-- Mostrar botão Help (.T.)
                        , lValFilGC      ; //-- Legado - não usado
                        ) AS Logical
```

## 🔧 Estrutura do Array de Perguntas

O array `aPergs` segue a mesma estrutura do SX1, com 42 posições:

```advpl
// 01 /02 /03     /04     /05     /06      /07  /08     /09     /10    /11 /12   /13   /14   /15   /16   /17   /18   /19   /20   /21   /22   /23   /24   /25   /26   /27   /28   /29   /30   /31   /32   /33   /34   /35   /36   /37   /38/39  /40 /41 /42
// Grp/Ord/Desc.Pt/Desc.Es/Desc.En/Variavel/Tipo/Tamanho/Decimal/Presel/GSC/Valid/Var01/Def1P/Def1E/Def1I/Cnt01/Var02/Def2P/Def2E/Def2I/Cnt02/Var03/Def3P/Def3E/Def3I/Cnt03/Var04/Def4P/Def4E/Def4I/Cnt04/Var05/Def5P/Def5E/Def5I/Cnt05/F3/Pyme/SXG/Hlp/Pict
```

### Campos Principais:
- **[03]** - Descrição da pergunta (Português)
- **[06]** - Variável associada (opcional)
- **[07]** - Tipo do campo (C/N/D/L)
- **[08]** - Tamanho
- **[09]** - Decimais (para numérico)
- **[11]** - Tipo especial (G=Geral, C=Combo, R=Range, F=File)
- **[12]** - Validação
- **[38]** - Consulta F3
- **[41]** - Help
- **[42]** - Picture

## 💡 Exemplos de Uso

### 1. Exemplo Básico

```advpl
User Function TestePergunte()
	
	//-- Declaração de Variáveis ----------------------------------------------
    Local aPergs := {}
    Local cPerg  := "TESTE01"
    
    //-- Pergunta simples de data
    aAdd(aPergs, {,,"Data de ?",,,,"D",08,0,,"G"})
    aAdd(aPergs, {,,"Data até ?",,,,"D",08,0,,"G"})
    
    If U_CXPergunte(cPerg,.T.,"Parâmetros de Data",,,,aPergs)
        ConOut("Data De: " + DtoC(MV_PAR01))
        ConOut("Data Até: " + DtoC(MV_PAR02))
    EndIf

Return
```

### 2. Exemplo com Range de Filiais

```advpl
User Function TesteRange()
	
	//-- Declaração de Variáveis ----------------------------------------------
    Local aPergs := {}
    
    //-- Range de filiais com validação
    aAdd(aPergs, {,,"Filiais ?",,,,"C",90,0,,"R","NaoVazio()",,"",,,;
                  "RA_FILIAL",,"",,,"",,"",,,,,"",,,,,"",,,,"XM0",,,".RHFILIAL."})
    
    If U_CXPergunte("RANGE01", .T., "Seleção de Filiais", ,,, aPergs)
        ConOut("Filiais selecionadas: " + MV_PAR01)
    EndIf

Return
```

### 3. Exemplo com Combo (Múltipla Escolha)

```advpl
User Function TesteCombo()
	
	//-- Declaração de Variáveis ----------------------------------------------
    Local aPergs := {}
    
    //-- Combo com opções predefinidas
    aAdd(aPergs, {,,"Tipo Relatório ?",,,,"N",01,0,3,"C",,;
                  ,"Sintético",,,,,"Analítico",,,,,"Ambos"})
    
    If U_CXPergunte("COMBO01", .T., "Tipo de Relatório", ,,, aPergs)
        ConOut("Opção selecionada: " + cValToChar(MV_PAR01))
    EndIf

Return
```

### 4. Exemplo com Seleção de Arquivo

```advpl
User Function TesteArquivo()
	
	//-- Declaração de Variáveis ----------------------------------------------
    Local aPergs := {}
    
    //-- Seleção de arquivo CSV
    aAdd(aPergs, {,,"Arquivo",,,,"C",100,0,,"F","U_ValidaArq()",;
                  ,GETF_LOCALHARD,,,"",,,"*.csv",,,"C:\Temp\",,,,,.T.,,,,,.F.})
    
    If U_CXPergunte("ARQUIVO01", .T., "Selecionar Arquivo", ,,, aPergs)
        ConOut("Arquivo selecionado: " + MV_PAR01)
    EndIf

Return
```

### 5. Exemplo com Variáveis Personalizadas

```advpl
User Function TesteVariaveis()
	
	//-- Declaração de Variáveis ----------------------------------------------
    Local aPergs := {}
    
	Private dDataDe   := CtoD("01/01/25")  //-- Vinculada ao MV_PAR01
    Private dDataAte  := Date()            //-- Vinculada ao MV_PAR02
    
    aAdd(aPergs, {,,"Data de ?",,,,"dDataDe","D",08,0,,"G"})
    aAdd(aPergs, {,,"Data até ?",,,,"dDataAte","D",08,0,,"G"})
    
    If U_CXPergunte("VAR01", .T., "Datas com Variáveis", ,,, aPergs)
        ConOut("dDataDe: " + DtoC(dDataDe))    //-- Automaticamente atualizada
        ConOut("dDataAte: " + DtoC(dDataAte))  //-- Automaticamente atualizada
    EndIf

Return
```

## 🎯 Tipos de Campo Suportados

### Campos Básicos
- **'G'** - Campo geral (padrão)
- **'C'** - Combo/múltipla escolha
- **'R'** - Range (intervalos e listas)
- **'F'** - Seleção de arquivo

### Tipos de Dados
- **'C'** - Caractere
- **'N'** - Numérico
- **'D'** - Data
- **'L'** - Lógico

## 📚 Validações

### Validação por String
```advpl
aAdd(aPergs, {,,"Campo",,,,"C",10,0,,"G","NaoVazio().And.ExistCpo('SA1')"})  //-- [12] cValid
```

### Validação por CodeBlock
```advpl
aAdd(aPergs, {,,"Campo",,,,"C",10,0,,"G",{|| !Empty(MV_PAR01)}})  //-- [12] bValid
```

## 🆘 Sistema de Help

### Help Simples (String)
```advpl
aAdd(aPergs, {,,"Campo",,,,"C",10,0,,"G",,,,,,,,,,,,,,,,,,,,,,,,,,,,,,{"Informe o código do cliente"}}) //-- [41] aHelp
```

### Help do Sistema
```advpl
aAdd(aPergs, {,,"Campo",,,,"C",10,0,,"G",,,,,,,,,,,,,,,,,,,,,,,,,,,,,,".SA1CODIGO."}) //-- [41] cHelp
```

## 🔄 Carregamento Automático do SX1

Se o array `aPergs` não for informado, a função carrega automaticamente as perguntas do SX1:

```advpl
//-- Carrega perguntas do grupo "MATA010" do SX1
If U_CXPergunte("MATA010", .T., "Cadastro de Produtos")
    //-- Processamento...
EndIf
```

## ⚙️ Recursos Avançados

### Quebra de Linha no Título
```advpl
U_CXPergunte(cPerg, .T., cTitulo, ,,, aPergs, .T.) //-- lBreakLine = .T.
```

### Desabilitar Help
```advpl
U_CXPergunte(cPerg, .T., cTitulo, ,,, aPergs, .F., .F.) //-- lHasHelp = .F.
```

### Não Carrega Perguntas Salvas no Perfil de Usuário
```advpl
U_CXPergunte(cPerg, .T., cTitulo, ,,, aPergs, .F., .T., .F.) //-- lUseProf = .F.
```

## 🔍 Range - Funcionalidade Especial

O tipo Range ('R') permite definir intervalos e listas:
- **Intervalos**: `001-005;010-015`
- **Valores únicos**: `001;003;007`
- **Misto**: `001-005;007;010-015`

Exemplo de range de centros de custo:
```
'001001'-'001010';'002001';'003001'-'003005'
```

## 📄 Licença

Este software é fornecido "TAL COMO ESTÁ", sem garantia de qualquer tipo. Use por sua conta e risco.

---

## 👨‍💻 Autor

**Cirilo Rocha**  
CX Consultoria  
Data: 30/09/2025 v1.20

---

## 🔗 Arquivos Relacionados

- **CXPergunte.tlpp** - Função principal
