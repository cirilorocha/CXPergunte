# CXPergunte - Função Personalizada para Parâmetros

## 📋 Descrição

A função **CXPergunte** é uma versão aprimorada da função padrão `Pergunte()` do Protheus/TOTVS, oferecendo maior flexibilidade na criação de telas de parâmetros. Permite definir perguntas dinamicamente através de arrays, sem necessidade de criar registros no SX1.

Exemplo1:

<img width="683" height="708" alt="image" src="https://github.com/user-attachments/assets/8051cff4-8bd3-4d26-8cab-b232857f9550" />



Exemplo2:

<img width="686" height="262" alt="image" src="https://github.com/user-attachments/assets/6bf6eaf2-aefc-4cc4-8fd8-2a1a09c91b3f" />


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

## 🔧 CXMakeExp - Parser de Parâmetros para Expressões SQL/AdvPL

### 📋 Descrição
A função `U_CXMakeExp` é uma ferramenta avançada de conversão de parâmetros do tipo RANGE em expressões SQL ou AdvPL, funcionando de forma análoga às funções padrão `MakeSqlExpr()` e `MakeAdvplExpr()` do Protheus, para uso em conjunto com a função CXPergunte().

### ✨ Funcionalidades Principais

#### 🎯 **Conversão Inteligente de Parâmetros**
- **Tipo RANGE**: Processa parâmetros de usuário do tipo "R" (Range)
- **Dupla Sintaxe**: Gera tanto expressões SQL quanto AdvPL
- **Parser Avançado**: Interpreta sintaxe complexa de ranges e listas

#### 🔍 **Tipos de Expressão Suportados**

**1. Lista de Valores (IN)**
```advpl
// Entrada: '000001;000003;000005'
// SQL:     CAMPO IN ('000001','000003','000005')  
// AdvPL:   CAMPO $ "'000001','000003','000005'"
```

**2. Intervalos (BETWEEN)**  
```advpl
// Entrada: '000001-000005;000010-000015'
// SQL:     CAMPO BETWEEN '000001' AND '000005' 
//          OR CAMPO BETWEEN '000010' AND '000015'
// AdvPL:   (CAMPO >= '000001' .AND. CAMPO <= '000005') 
//          .OR. (CAMPO >= '000010' .AND. CAMPO <= '000015')
```

**3. Sintaxe Mista**
```advpl
// Entrada: '000001-000005;000007;000010-000012;000015'  
// Combina BETWEEN e IN na mesma expressão
```

### 🚀 Como Usar

#### Sintaxe Básica
```advpl
U_CXMakeExp(aPergs, lSQL)
```

#### Parâmetros
- **aPergs** (Array): Array de parâmetros utilizado no CXPergunte()
- **lSQL** (Logical): 
  - `.T.` = Gera expressão SQL
  - `.F.` = Gera expressão AdvPL

#### Exemplo Prático
```advpl
//-- Configuração do parâmetro
aPergs := {}
aAdd(aPergs, {'01','Filiais','','','','Filiais','','','','R','SA1->A1_FILIAL',...})

//-- Usuário digita: '01-05;07;10-12'
MV_PAR01 := '01-05;07;10-12'

//-- Chamada da função
U_CXMakeExp(aPergs, .T.) // Para SQL

//-- Resultado em MV_PAR01:
// "SA1.A1_FILIAL BETWEEN '01' AND '05' 
//  OR SA1.A1_FILIAL IN ('07') 
//  OR SA1.A1_FILIAL BETWEEN '10' AND '12'"
```

### 🔧 Funcionalidades Técnicas

#### **Parser Inteligente de String**
- **Aspas de Proteção**: Suporta valores entre aspas simples
- **Delimitadores**: Usa `;` para separar valores e `-` para ranges  

#### **Gerenciamento de Variáveis**
- **Múltiplas Variáveis**: Preenche MV_PAR + variáveis auxiliares
- **Sincronização**: Mantém todas as variáveis vinculadas atualizadas
- **Flexibilidade**: Suporta campos customizados via nX1_CNT01

#### **Tratamento de Casos Especiais**  
- **Parâmetro Vazio**: Retorna expressão sempre verdadeira
  - SQL: `( 1 = 1 ) /*-- CAMPO SEM FILTRO --*/`
  - AdvPL: `AllwaysTrue()`
- **Validação de Tipo**: Processa apenas parâmetros tipo 'R'

### 📊 Estrutura de Dados

#### Array de Parâmetros (aPergs)
```advpl
//-- Estrutura esperada (compatível com SX1)
aPergs[nX][nX1_GSC]    //-- Tipo do parâmetro ('R' para Range)
aPergs[nX][nX1_CNT01]  //-- Nome do campo para a expressão  
aPergs[nX][nX1_VAR01]  //-- Variável auxiliar 1 (opcional)
aPergs[nX][nX1_VAR02]  //-- Variável auxiliar 2 (opcional)
```

### ⚡ **Vantagens sobre Funções Padrão**

| Recurso | MakeSqlExpr() | CXMakeExp() |
|---------|---------------|-------------|
| **Múltiplas Variáveis** | ❌ | ✅ |
| **Expressão Vazia** | ❌ | ✅ |
| **Dual Output** | ❌ | ✅ |

### 🎯 Casos de Uso Típicos

#### **1. Relatórios com Filtros Avançados**
```advpl
//-- Filtro de filiais: '01-05;SP01;RJ01-RJ05'  
//-- Filtro de produtos: 'A001-A999;B500;C100-C200'
```

#### **2. Consultas Dinâmicas**
```advpl  
//-- Query gerada automaticamente com filtros complexos
cQuery := "SELECT * FROM SA1 WHERE " + MV_PAR01
```

#### **3. Processamento Batch**
```advpl
//-- Validação AdvPL em loops  
If &(MV_PAR01) // Expressão gerada pela função
    //-- Processa registro
EndIf
```

### 🛡️ **Tratamento de Erros e Validações**

- ✅ **Validação de Tipo**: Verifica se parâmetro é tipo 'R'
- ✅ **Campo Obrigatório**: Valida presença do campo em nX1_CNT01  
- ✅ **Sintaxe Segura**: Parser robusto contra caracteres especiais
- ✅ **Fallback**: Expressão padrão para casos vazios

### 📈 **Performance e Otimização**

- **SQL Otimizado**: Gera expressões que aproveitam índices
- **Cache de Variáveis**: Reutiliza variáveis já processadas

---

### 💡 **Exemplo Completo de Implementação**

```advpl
User Function ExemploRange()

    //-- Declaração de Variáveis ----------------------------------------------
    Local aPergs := {}                              AS Array
	Local cPerg  := 'ExemploRange'                  AS Character
    
    //-- Configura parâmetros
//                03                     07 08 09 11  12              14    17            18   22  24     29     34     38      41
//                Descrição              Tp Tm Dc GCS Validacao       D1    Cnt1          D2  Cnt2 D3     D4     D5     F3      Hlp
	aAdd(aPergs,{,,'Filiais ?'			,,,,'C',90,0,,'R','NaoVazio()'	,'cFiliais','',,,'RA_FILIAL'	,,'',,,'',,'',,,,,'',,,,,'',,,,'XM0',,,'.RHFILIAL.'})	//RANGE
	
	If .Not. U_CXPergunte(cPerg,.T.,'Informe os parâmetros de filtro',,,,aPergs)
		Return
	EndIf	
	
	U_CXMakeExp(aPergs,.T./*lSQL*/)	//-- Converte os parâmetros tipo RANGE em expressão SQL ou ADVPL
    
    // MV_PAR01 agora contém:
    // "SA1.A1_COD BETWEEN '000001' AND '000100' 
    //  OR SA1.A1_COD IN ('000150') 
    //  OR SA1.A1_COD BETWEEN '000200' AND '000300'"
    
    // Usa na query
    cQuery := "SELECT * FROM " + RetSqlName('SA1') + " SA1 "
    cQuery += "WHERE " + MV_PAR01
    
Return
```

**🚀 Esta função revolutiona o tratamento de parâmetros RANGE no Protheus, oferecendo flexibilidade e poder de uma sintaxe avançada para filtros complexos!**

## 📄 Licença

Este software é fornecido "TAL COMO ESTÁ", sem garantia de qualquer tipo. Use por sua conta e risco.

---
