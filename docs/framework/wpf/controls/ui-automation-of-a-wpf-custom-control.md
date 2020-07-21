---
title: カスタム コントロールの UI オートメーション
ms.date: 03/30/2017
dev_langs:
- csharp
- vb
helpviewer_keywords:
- custom controls [WPF], applying UI automation
- accessibility [WPF], applying to custom controls
- custom controls [WPF], improving accessibility
- UI Automation [WPF], using with custom controls
ms.assetid: 47b310fc-fbd5-4ce2-a606-22d04c6d4911
ms.openlocfilehash: 97db94215220ac2a68e0395bd63b7a874a745a48
ms.sourcegitcommit: 7980a91f90ae5eca859db7e6bfa03e23e76a1a50
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2020
ms.locfileid: "81243246"
---
# <a name="ui-automation-of-a-wpf-custom-control"></a>WPF カスタム コントロールの UI オートメーション
[!INCLUDE[TLA#tla_uiautomation](../../../../includes/tlasharptla-uiautomation-md.md)] は、オートメーション クライアントが使用してさまざまなプラットフォームやフレームワークのユーザー インターフェイスを調査または操作できる、単一の汎用的なインターフェイスを提供します。 [!INCLUDE[TLA2#tla_uiautomation](../../../../includes/tla2sharptla-uiautomation-md.md)] では、品質保証 (テスト) のコードとスクリーン リーダーなどのユーザー補助アプリケーションの両方を使用して、ユーザー インターフェイス要素を調べて、他のコードからその要素を使用してユーザーの操作をシミュレーションできます。 すべてのプラットフォームにまたがる [!INCLUDE[TLA2#tla_uiautomation](../../../../includes/tla2sharptla-uiautomation-md.md)] について詳しくは、「ユーザー補助機能」を参照してください。  
  
 このトピックでは、WPF アプリケーションで動作するカスタム コントロールのサーバー側 UI オートメーション プロバイダーを実装する方法について説明します。 WPF は、ユーザー インターフェイス要素のツリーに対応するピア オートメーション オブジェクトのツリーを使用して [!INCLUDE[TLA2#tla_uiautomation](../../../../includes/tla2sharptla-uiautomation-md.md)] をサポートします。 テスト コードおよびユーザー補助機能を提供するアプリケーションは、オートメーション ピア オブジェクトを直接 (プロセス内コードに対して)、または [!INCLUDE[TLA2#tla_uiautomation](../../../../includes/tla2sharptla-uiautomation-md.md)] が提供する汎用のインターフェイスを介して使用できます。  

<a name="AutomationPeerClasses"></a>
## <a name="automation-peer-classes"></a>オートメーション ピア クラス  
 WPF コントロールでは、<xref:System.Windows.Automation.Peers.AutomationPeer> から派生するピア クラスのツリーを通じて [!INCLUDE[TLA2#tla_uiautomation](../../../../includes/tla2sharptla-uiautomation-md.md)] がサポートされます。 規則上、ピア クラス名は、コントロールのクラス名で始まり、"AutomationPeer" で終わります。 たとえば<xref:System.Windows.Automation.Peers.ButtonAutomationPeer> は <xref:System.Windows.Controls.Button> コントロール クラスのピア クラスです。 ピア クラスは、[!INCLUDE[TLA2#tla_uiautomation](../../../../includes/tla2sharptla-uiautomation-md.md)] コントロール型とほぼ同等ですが、WPF 要素に固有のものです。 [!INCLUDE[TLA2#tla_uiautomation](../../../../includes/tla2sharptla-uiautomation-md.md)] インターフェイスを介して WPF アプリケーションにアクセスするオートメーション コードは、オートメーション ピアを直接使用しませんが、同じプロセス空間のオートメーション コードは、オートメーション ピアを直接使用できます。  
  
<a name="BuiltInAutomationPeerClasses"></a>
## <a name="built-in-automation-peer-classes"></a>組み込みのオートメーション ピア クラス  
 要素は、ユーザーからのインターフェイス アクティビティを受け入れる場合、またはスクリーン リーダー アプリケーションのユーザーが必要とする情報が含まれている場合、オートメーション ピア クラスを実装します。 すべての WPF のビジュアル要素にオートメーション ピアがあるわけではありません。 オートメーション ピアが実装されているクラスの例は、<xref:System.Windows.Controls.Button>、<xref:System.Windows.Controls.TextBox>、<xref:System.Windows.Controls.Label> などです。 オートメーション ピアが実装されていないクラスの例は、<xref:System.Windows.Controls.Decorator> から派生されたクラス (<xref:System.Windows.Controls.Border> など)、および <xref:System.Windows.Controls.Panel> に基づくクラス (<xref:System.Windows.Controls.Grid> や <xref:System.Windows.Controls.Canvas> など) です。  
  
 基底クラス <xref:System.Windows.Controls.Control> には、対応するピア クラスはありません。 <xref:System.Windows.Controls.Control> から派生したカスタム コントロールにピア クラスを対応させる必要がある場合は、カスタム ピア クラスを <xref:System.Windows.Automation.Peers.FrameworkElementAutomationPeer> から派生させる必要があります。  
  
<a name="SecurityConsiderations"></a>
## <a name="security-considerations-for-derived-peers"></a>派生ピアのセキュリティに関する考慮事項  
 オートメーション ピアは、部分信頼環境で実行する必要があります。 UIAutomationClient アセンブリ内のコードは部分信頼環境で動作するように構成されていないため、オートメーション ピア コードでそのアセンブリを参照しないでください。 代わりに、UIAutomationTypes アセンブリのクラスを使用する必要があります。 たとえば、UIAutomationTypes アセンブリの <xref:System.Windows.Automation.AutomationElementIdentifiers> クラスを使用する必要があります。これは、UIAutomationClient アセンブリの <xref:System.Windows.Automation.AutomationElement> に対応します。 オートメーション ピア コードで UIAutomationTypes アセンブリを参照すると安全です。  
  
<a name="PeerNavigation"></a>
## <a name="peer-navigation"></a>ピア ナビゲーション  
 オートメーション ピアを見つけた後、オブジェクトの <xref:System.Windows.Automation.Peers.AutomationPeer.GetChildren%2A> メソッドおよび <xref:System.Windows.Automation.Peers.AutomationPeer.GetParent%2A> メソッドを呼び出すことによって、プロセス内コードでピア ツリー内を移動できます。 コントロール内の WPF 要素間の移動は、ピアでの <xref:System.Windows.Automation.Peers.AutomationPeer.GetChildrenCore%2A> メソッドの実装によってサポートされています。 UI オートメーション システムは、このメソッドを呼び出して、コントロール内に含まれるサブ要素のツリーを構築します。たとえば、リスト ボックス内の項目を一覧表示します。 既定の <xref:System.Windows.Automation.Peers.UIElementAutomationPeer.GetChildrenCore%2A?displayProperty=nameWithType> メソッドでは、要素のビジュアル ツリーが走査されて、オートメーション ピアのツリーが構築されます。 カスタム コントロールは、このメソッドをオーバーライドして子要素をオートメーション クライアントに公開し、情報を伝達するまたはユーザーによる操作を許可する要素のオートメーション ピアを返します。  
  
<a name="Customizations"></a>
## <a name="customizations-in-a-derived-peer"></a>派生ピアのカスタマイズ  
 <xref:System.Windows.UIElement> および <xref:System.Windows.ContentElement> から派生したすべてのクラスには、保護された仮想メソッド <xref:System.Windows.UIElement.OnCreateAutomationPeer%2A> が含まれています。 WPF では、<xref:System.Windows.UIElement.OnCreateAutomationPeer%2A> が呼び出されて、各コントロールのオートメーション ピア オブジェクトが取得されます。 オートメーション コードは、ピアを使用して、コントロールの特性と機能に関する情報を取得し、対話型の使用をシミュレートします。 オートメーションをサポートするカスタム コントロールでは、<xref:System.Windows.UIElement.OnCreateAutomationPeer%2A> をオーバーライドし、<xref:System.Windows.Automation.Peers.AutomationPeer> の派生クラスのインスタンスを返す必要があります。 たとえば、カスタム コントロールが <xref:System.Windows.Controls.Primitives.ButtonBase> クラスから派生する場合、<xref:System.Windows.UIElement.OnCreateAutomationPeer%2A> によって返されるオブジェクトは、<xref:System.Windows.Automation.Peers.ButtonBaseAutomationPeer> から派生している必要があります。  
  
 カスタム コントロールを実装する場合、カスタム コントロールに固有の動作を説明する基本オートメーション ピア クラスからの「コア」メソッドをオーバーライドする必要があります。  
  
### <a name="override-oncreateautomationpeer"></a>OnCreateAutomationPeer のオーバーライド  
 カスタム コントロールの <xref:System.Windows.UIElement.OnCreateAutomationPeer%2A> メソッドをオーバーライドし、<xref:System.Windows.Automation.Peers.AutomationPeer> から直接または間接に派生する必要があるプロバイダー オブジェクトを返すようにします。  
  
### <a name="override-getpattern"></a>GetPattern のオーバーライド  
 オートメーション ピアは、サーバー側の [!INCLUDE[TLA2#tla_uiautomation](../../../../includes/tla2sharptla-uiautomation-md.md)] プロバイダーの実装の一部を簡略化しますが、カスタム コントロール オートメーション ピアでもパターン インターフェイスを処理する必要があります。 WPF 以外のプロバイダーと同様に、ピアでは、<xref:System.Windows.Automation.Provider?displayProperty=nameWithType> 名前空間内のインターフェイスの実装を提供することによって、コントロール パターンをサポートします (<xref:System.Windows.Automation.Provider.IInvokeProvider> など)。 コントロール パターンのインターフェイスは、ピア自体または別のオブジェクトによって実装できます。 <xref:System.Windows.Automation.Peers.AutomationPeer.GetPattern%2A> のピアでの実装では、指定されたパターンをサポートするオブジェクトが返されます。 [!INCLUDE[TLA2#tla_uiautomation](../../../../includes/tla2sharptla-uiautomation-md.md)] のコードでは、<xref:System.Windows.Automation.Peers.UIElementAutomationPeer.GetPattern%2A> メソッドが呼び出されて、<xref:System.Windows.Automation.Peers.PatternInterface> 列挙値が指定されます。 <xref:System.Windows.Automation.Peers.UIElementAutomationPeer.GetPattern%2A> のオーバーライドでは、指定されたパターンが実装されているオブジェクトを返す必要があります。 コントロールにパターンのカスタム実装がない場合、<xref:System.Windows.Automation.Peers.AutomationPeer.GetPattern%2A> の基本型の実装を呼び出し、その実装を取得できます。パターンがこのコントロール型でサポートされていない場合は null が取得されます。 たとえば、カスタムの NumericUpDown コントロールを範囲内の値に設定できるため、その [!INCLUDE[TLA2#tla_uiautomation](../../../../includes/tla2sharptla-uiautomation-md.md)] ピアでは <xref:System.Windows.Automation.Provider.IRangeValueProvider> インターフェイスを実装します。 次の例では、<xref:System.Windows.Automation.Peers.PatternInterface.RangeValue?displayProperty=nameWithType> の値に応答するようにピアの <xref:System.Windows.Automation.Peers.UIElementAutomationPeer.GetPattern%2A> メソッドをオーバーライドする方法を示します。  
  
 [!code-csharp[CustomControlNumericUpDown#GetPattern](~/samples/snippets/csharp/VS_Snippets_Wpf/CustomControlNumericUpDown/CSharp/CustomControlLibrary/NumericUpDown.cs#getpattern)]
 [!code-vb[CustomControlNumericUpDown#GetPattern](~/samples/snippets/visualbasic/VS_Snippets_Wpf/CustomControlNumericUpDown/visualbasic/customcontrollibrary/numericupdown.vb#getpattern)]  
  
 <xref:System.Windows.Automation.Peers.UIElementAutomationPeer.GetPattern%2A> メソッドでは、パターン プロバイダーとしてサブ要素を指定することもできます。 次のコードでは、<xref:System.Windows.Controls.ItemsControl> においてスクロール パターンの処理を内部の <xref:System.Windows.Controls.ScrollViewer> コントロールのピアに転送する方法を示します。  
  
```csharp  
public override object GetPattern(PatternInterface patternInterface)  
{  
    if (patternInterface == PatternInterface.Scroll)  
    {  
        ItemsControl owner = (ItemsControl) base.Owner;  
  
        // ScrollHost is internal to the ItemsControl class  
        if (owner.ScrollHost != null)  
        {  
            AutomationPeer peer = UIElementAutomationPeer.CreatePeerForElement(owner.ScrollHost);  
            if ((peer != null) && (peer is IScrollProvider))  
            {  
                peer.EventsSource = this;  
                return (IScrollProvider) peer;  
            }  
        }  
    }  
    return base.GetPattern(patternInterface);  
}  
```  
  
```vb  
Public Class Class1  
    Public Overrides Function GetPattern(ByVal patternInterface__1 As PatternInterface) As Object  
        If patternInterface1 = PatternInterface.Scroll Then  
            Dim owner As ItemsControl = DirectCast(MyBase.Owner, ItemsControl)  
  
            ' ScrollHost is internal to the ItemsControl class  
            If owner.ScrollHost IsNot Nothing Then  
                Dim peer As AutomationPeer = UIElementAutomationPeer.CreatePeerForElement(owner.ScrollHost)  
                If (peer IsNot Nothing) AndAlso (TypeOf peer Is IScrollProvider) Then  
                    peer.EventsSource = Me  
                    Return DirectCast(peer, IScrollProvider)  
                End If  
            End If  
        End If  
        Return MyBase.GetPattern(patternInterface1)  
    End Function  
End Class  
```  
  
 パターン処理に対してサブ要素を指定するため、このコードでは、サブ要素オブジェクトを取得し、<xref:System.Windows.Automation.Peers.UIElementAutomationPeer.CreatePeerForElement%2A> メソッドを使用してピアを作成し、新しいピアの <xref:System.Windows.Automation.Peers.AutomationPeer.EventsSource%2A> プロパティに現在のピアを設定して、新しいピアを返しています。 サブ要素で <xref:System.Windows.Automation.Peers.AutomationPeer.EventsSource%2A> を設定すると、そのサブ要素がオートメーション ピア ツリーに表示されなくなり、そのサブ要素によって生成されるすべてのイベントを <xref:System.Windows.Automation.Peers.AutomationPeer.EventsSource%2A> で指定されているコントロールから発信されたようにすることが指定されます。 <xref:System.Windows.Controls.ScrollViewer> コントロールはオートメーション ツリーに表示されず、それによって生成されるスクロール イベントは <xref:System.Windows.Controls.ItemsControl> オブジェクトから発信されたように見えます。  
  
### <a name="override-core-methods"></a>「コア」メソッドのオーバーライド  
 オートメーション コードは、ピア クラスのパブリック メソッドを呼び出して、コントロールに関する情報を取得します。 コントロールに関する情報を提供するには、コントロールの実装が基本オートメーション ピア クラスで提供されているものとは異なる場合に、"Core" で終わる名前を持つ各メソッドをオーバーライドします。 少なくとも、コントロールでは、次の例で示すように、<xref:System.Windows.Automation.Peers.AutomationPeer.GetClassNameCore%2A> メソッドと <xref:System.Windows.Automation.Peers.AutomationPeer.GetAutomationControlTypeCore%2A> メソッドを実装する必要があります。  
  
 [!code-csharp[CustomControlNumericUpDown#CoreOverrides](~/samples/snippets/csharp/VS_Snippets_Wpf/CustomControlNumericUpDown/CSharp/CustomControlLibrary/NumericUpDown.cs#coreoverrides)]
 [!code-vb[CustomControlNumericUpDown#CoreOverrides](~/samples/snippets/visualbasic/VS_Snippets_Wpf/CustomControlNumericUpDown/visualbasic/customcontrollibrary/numericupdown.vb#coreoverrides)]  
  
 <xref:System.Windows.Automation.Peers.AutomationPeer.GetAutomationControlTypeCore%2A> の実装では、<xref:System.Windows.Automation.ControlType> の値を返すことによって、コントロールを説明します。 <xref:System.Windows.Automation.ControlType.Custom?displayProperty=nameWithType> を返してもかまいませんが、より具体的なコントロール型の 1 つを返すことによってコントロールが正確に説明される場合は、そうする必要があります。 <xref:System.Windows.Automation.ControlType.Custom?displayProperty=nameWithType> の戻り値では、プロバイダーで [!INCLUDE[TLA2#tla_uiautomation](../../../../includes/tla2sharptla-uiautomation-md.md)] を実装するための追加作業が必要です。また、[!INCLUDE[TLA2#tla_uiautomation](../../../../includes/tla2sharptla-uiautomation-md.md)] のクライアント製品では、コントロールの構造、キーボード操作、および可能なコントロール パターンを予測することはできません。  
  
 コントロールにデータの内容が含まれるか、またはコントロールのユーザー インターフェイスで対話型のロールを果たすか (またはその両方か) を示すように、<xref:System.Windows.Automation.Peers.AutomationPeer.IsContentElementCore%2A> メソッドと <xref:System.Windows.Automation.Peers.AutomationPeer.IsControlElementCore%2A> メソッドを実装します。 既定では、両方のメソッドが `true` を返します。 これらの設定は、オートメーション ツリーをフィルター処理するこれらのメソッドを使用できるスクリーン リーダーなどのオートメーション ツールの使いやすさを向上します。 <xref:System.Windows.Automation.Peers.AutomationPeer.GetPattern%2A> メソッドでパターン処理をサブ要素ピアに転送した場合は、サブ要素ピアの <xref:System.Windows.Automation.Peers.AutomationPeer.IsControlElementCore%2A> メソッドで、false を返し、オートメーション ツリーにサブ要素ピアが表示されないようにできます。 たとえば、<xref:System.Windows.Controls.ListBox> でのスクロールは <xref:System.Windows.Controls.ScrollViewer> によって処理され、<xref:System.Windows.Automation.Peers.ListBoxAutomationPeer> に関連付けられている <xref:System.Windows.Automation.Peers.ScrollViewerAutomationPeer> の <xref:System.Windows.Automation.Peers.AutomationPeer.GetPattern%2A> メソッドでは <xref:System.Windows.Automation.Peers.PatternInterface.Scroll?displayProperty=nameWithType> のオートメーション ピアが返されます。したがって、<xref:System.Windows.Automation.Peers.ScrollViewerAutomationPeer> はがートメーション ツリーに表示されないように、<xref:System.Windows.Automation.Peers.ScrollViewerAutomationPeer> の<xref:System.Windows.Automation.Peers.AutomationPeer.IsControlElementCore%2A> メソッドでは `false` を返します。  
  
 オートメーション ピアは、コントロールに適切な既定値を提供する必要があります。 コントロールを参照する XAML では、<xref:System.Windows.Automation.AutomationProperties> 属性を含めることによって、コア メソッドのピアによる実装をオーバーライドできることに注意してください。 たとえば、次の XAML は、カスタマイズされた [!INCLUDE[TLA2#tla_uiautomation](../../../../includes/tla2sharptla-uiautomation-md.md)] プロパティが 2 つあるボタンを作成します。  
  
```xaml  
<Button AutomationProperties.Name="Special"
    AutomationProperties.HelpText="This is a special button."/>  
```  
  
### <a name="implement-pattern-providers"></a>パターン プロバイダーの実装  
 カスタム プロバイダーによって実装されるインターフェイスは、所有している要素が <xref:System.Windows.Controls.Control> から直接派生している場合は、明示的に宣言されます。 たとえば、次のコードでは、範囲の値を実装する <xref:System.Windows.Controls.Control> のピアが宣言されています。  
  
```csharp  
public class RangePeer1 : FrameworkElementAutomationPeer, IRangeValueProvider { }  
```  
  
```vb  
Public Class RangePeer1  
    Inherits FrameworkElementAutomationPeer  
    Implements IRangeValueProvider  
End Class  
```  
  
 所有しているコントロールが <xref:System.Windows.Controls.Primitives.RangeBase> などの特定の型のコントロールから派生している場合、同等の派生ピア クラスからピアを派生できます。 この場合、ピアの派生元の <xref:System.Windows.Automation.Peers.RangeBaseAutomationPeer> によって、<xref:System.Windows.Automation.Provider.IRangeValueProvider> の基本的な実装が提供されます。 次のコードは、そのようなピアの宣言を示します。  
  
```csharp  
public class RangePeer2 : RangeBaseAutomationPeer { }  
```  
  
```vb  
Public Class RangePeer2  
    Inherits RangeBaseAutomationPeer  
End Class  
```  
  
実装の例については、NumericUpDown カスタム コントロールが実装および使用されている [C#](https://github.com/dotnet/docs/tree/master/samples/snippets/csharp/VS_Snippets_Wpf/CustomControlNumericUpDown/CSharp) または [Visual Basic](https://github.com/dotnet/docs/tree/master/samples/snippets/visualbasic/VS_Snippets_Wpf/CustomControlNumericUpDown/visualbasic) のソース コードを参照してください。  
  
### <a name="raise-events"></a>イベントを発生させる  
 オートメーション クライアントは、オートメーション イベントをサブスクライブできます。 カスタム コントロールでは、<xref:System.Windows.Automation.Peers.AutomationPeer.RaiseAutomationEvent%2A> メソッドを呼び出すことによって、コントロールの状態に対する変更を報告する必要があります。 同様に、プロパティの値が変更されたときは、<xref:System.Windows.Automation.Peers.AutomationPeer.RaisePropertyChangedEvent%2A> メソッドを呼び出します。 次のコードは、コントロール コード内からピア オブジェクトを取得し、イベントを発生させるメソッドを呼び出す方法を示しています。 最適化として、このコードは、このイベント型のリスナーがいるかどうかを判断します。 リスナーがいる場合にのみイベントを発生させると、不要なオーバーヘッドを回避し、コントロールの応答性を維持できます。  
  
 [!code-csharp[CustomControlNumericUpDown#RaiseEventFromControl](~/samples/snippets/csharp/VS_Snippets_Wpf/CustomControlNumericUpDown/CSharp/CustomControlLibrary/NumericUpDown.cs#raiseeventfromcontrol)]
 [!code-vb[CustomControlNumericUpDown#RaiseEventFromControl](~/samples/snippets/visualbasic/VS_Snippets_Wpf/CustomControlNumericUpDown/visualbasic/customcontrollibrary/numericupdown.vb#raiseeventfromcontrol)]  
  
## <a name="see-also"></a>関連項目

- [UI オートメーションの概要](../../ui-automation/ui-automation-overview.md)
- [サーバー側 UI オートメーション プロバイダーの実装](../../ui-automation/server-side-ui-automation-provider-implementation.md)
- [GitHub の NumericUpDown カスタム コントロール (C#)](https://github.com/dotnet/docs/tree/master/samples/snippets/csharp/VS_Snippets_Wpf/CustomControlNumericUpDown/CSharp)  
- [GitHub の NumericUpDown カスタム コントロール (Visual Basic)](https://github.com/dotnet/docs/tree/master/samples/snippets/visualbasic/VS_Snippets_Wpf/CustomControlNumericUpDown/visualbasic)
