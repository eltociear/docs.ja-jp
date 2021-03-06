---
title: 名前と値のペアの保持
ms.date: 07/20/2015
ms.assetid: 57ac2072-d9f5-432b-84f0-a889c62fd813
ms.openlocfilehash: ed9c7f0aae2fe646cd723321f45455f89dd7c370
ms.sourcegitcommit: 17ee6605e01ef32506f8fdc686954244ba6911de
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/22/2019
ms.locfileid: "74331664"
---
# <a name="maintaining-namevalue-pairs-visual-basic"></a>名前と値のペアの保持 (Visual Basic)
多くのアプリケーションでは、情報を名前と値のペアとして保持するのが最適な場合があります。 このような情報には、構成情報やグローバル設定などがあります。 [!INCLUDE[sqltecxlinq](~/includes/sqltecxlinq-md.md)] には、名前と値のペアのセットを簡単に保持できるようにするメソッドがあります。 情報を属性として保持することも、子要素のセットとして保持することもできます。  
  
 情報を属性として保持する場合と子要素として保持する場合の違いの 1 つは、要素の特定の名前を持つ属性は 1 つしか存在できないという制約が属性にはあることです。 この制限は子要素には適用されません。  
  
## <a name="setattributevalue-and-setelementvalue"></a>SetAttributeValue と SetElementValue  
 名前と値のペアの保持を容易にする 2 つのメソッドは、<xref:System.Xml.Linq.XElement.SetAttributeValue%2A> と <xref:System.Xml.Linq.XElement.SetElementValue%2A> です。 これらの 2 つのメソッドは、よく似たセマンティクスを持っています。  
  
 <xref:System.Xml.Linq.XElement.SetAttributeValue%2A> は、要素の属性を追加、変更、または削除できます。  
  
- 存在しない属性の名前を指定して <xref:System.Xml.Linq.XElement.SetAttributeValue%2A> を呼び出すと、新しい属性が作成され、その属性が指定した要素に追加されます。  
  
- 既存の属性の名前およびコンテンツを指定して <xref:System.Xml.Linq.XElement.SetAttributeValue%2A> を呼び出すと、その属性のコンテンツが指定したコンテンツに置き換えられます。  
  
- 既存の属性の名前を指定して <xref:System.Xml.Linq.XElement.SetAttributeValue%2A> を呼び出し、コンテンツに NULL を指定すると、その属性が親から削除されます。  
  
 <xref:System.Xml.Linq.XElement.SetElementValue%2A> は、要素の子要素を追加、変更、または削除できます。  
  
- 存在しない子要素の名前を指定して <xref:System.Xml.Linq.XElement.SetElementValue%2A> を呼び出すと、新しい要素が作成され、その要素が指定した要素に追加されます。  
  
- 既存の要素の名前およびコンテンツを指定して <xref:System.Xml.Linq.XElement.SetElementValue%2A> を呼び出すと、その要素のコンテンツが指定したコンテンツに置き換えられます。  
  
- 既存の要素の名前を指定して <xref:System.Xml.Linq.XElement.SetElementValue%2A> を呼び出し、コンテンツに NULL を指定すると、その要素が親から削除されます。  
  
## <a name="example"></a>例  
 次の例では、属性を持たない要素を作成します。 次に、<xref:System.Xml.Linq.XElement.SetAttributeValue%2A> メソッドを使用して名前と値のペアの一覧を作成して保持します。  
  
```vb  
' Create an element with no content.  
Dim root As XElement = <Root/>  
  
' Add a number of name/value pairs as attributes.  
root.SetAttributeValue("Top", 22)  
root.SetAttributeValue("Left", 20)  
root.SetAttributeValue("Bottom", 122)  
root.SetAttributeValue("Right", 300)  
root.SetAttributeValue("DefaultColor", "Color.Red")  
Console.WriteLine(root)  
  
' Replace the value of Top.  
root.SetAttributeValue("Top", 10)  
Console.WriteLine(root)  
  
' Remove DefaultColor.  
root.SetAttributeValue("DefaultColor", Nothing)  
Console.WriteLine(root)  
```  
  
 この例を実行すると、次の出力が生成されます。  
  
```xml  
<Root Top="22" Left="20" Bottom="122" Right="300" DefaultColor="Color.Red" />  
<Root Top="10" Left="20" Bottom="122" Right="300" DefaultColor="Color.Red" />  
<Root Top="10" Left="20" Bottom="122" Right="300" />  
```  
  
## <a name="example"></a>例  
 次の例では、子要素を持たない要素を作成します。 次に、<xref:System.Xml.Linq.XElement.SetElementValue%2A> メソッドを使用して名前と値のペアの一覧を作成して保持します。  
  
```vb  
' Create an element with no content.  
Dim root As XElement = <Root/>  
  
' Add a number of name/value pairs as elements.  
root.SetElementValue("Top", 22)  
root.SetElementValue("Left", 20)  
root.SetElementValue("Bottom", 122)  
root.SetElementValue("Right", 300)  
root.SetElementValue("DefaultColor", "Color.Red")  
Console.WriteLine(root)  
Console.WriteLine("----")  
  
' Replace the value of Top.  
root.SetElementValue("Top", 10)  
Console.WriteLine(root)  
Console.WriteLine("----")  
  
' Remove DefaultColor.  
root.SetElementValue("DefaultColor", Nothing)  
Console.WriteLine(root)  
```  
  
 この例を実行すると、次の出力が生成されます。  
  
```xml  
<Root>  
  <Top>22</Top>  
  <Left>20</Left>  
  <Bottom>122</Bottom>  
  <Right>300</Right>  
  <DefaultColor>Color.Red</DefaultColor>  
</Root>  
----  
<Root>  
  <Top>10</Top>  
  <Left>20</Left>  
  <Bottom>122</Bottom>  
  <Right>300</Right>  
  <DefaultColor>Color.Red</DefaultColor>  
</Root>  
----  
<Root>  
  <Top>10</Top>  
  <Left>20</Left>  
  <Bottom>122</Bottom>  
  <Right>300</Right>  
</Root>  
```  
  
## <a name="see-also"></a>参照

- <xref:System.Xml.Linq.XElement.SetAttributeValue%2A>
- <xref:System.Xml.Linq.XElement.SetElementValue%2A>
- [XML ツリーの変更 (LINQ to XML) (Visual Basic)](../../../../visual-basic/programming-guide/concepts/linq/modifying-xml-trees-linq-to-xml.md)
