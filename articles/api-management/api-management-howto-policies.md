<properties 
    pageTitle="Azure API 管理中的原則" 
    description="了解如何在 API 管理中建立、編輯和設定原則。" 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/02/2015" 
    ms.author="sdanie"/>


#Azure API 管理中的原則

在 Azure API 管理中，原則是系統的一項強大功能，可讓發行者透過組態來變更 API 的行為。 原則是「陳述式」的集合，會在 API 的要求或回應上循序執行。 常見的「陳述式」包括從 XML 至 JSON 的格式轉換，以及利用呼叫速率限制來限制開發人員傳入的呼叫數量。 還有許多現成的原則可用。

請參閱 [原則參考文件][] 原則陳述式及其設定的完整清單。

原則是在位於 API 取用者與受管理 API 之間的閘道內套用。 閘道會接收所有要求，然後通常原封不動地轉送至基礎 API。 不過，原則可以套用至輸入要求和輸出要求。

如果原則不另行指定，則可以在任何 API 管理原則中，使用原則運算式做為屬性值或文字值。 例如，某些原則 [控制流程][] 和 [設定變數][] 原則以原則運算式為基礎。 如需詳細資訊，請參閱 [進階原則][] 和 [原則運算式][]。

## <a name="scopes"> </a>如何設定原則
您可以設定原則，全域或範圍 [產品][], ，[API][] 或 [作業][]。 若要設定原則，請瀏覽至發佈者入口網站的原則編輯器。

![Policies menu][policies-menu]

原則編輯器包含三個主要區段：原則範圍 (上)、編輯原則的原則定義 (左) 和陳述式清單 (右)：

![Policies editor][policies-editor]

若要開始設定原則，您必須先選取要套用原則的範圍。 以下螢幕擷取畫面中 **入門** 選取產品。 請注意，原則名稱旁邊的正方形符號表示此層級上已套用原則。

![Scope][policies-scope]

因為已套用原則，定義檢視中會顯示組態。

![設定][policies-configure]

原則最初會以唯讀顯示。 為了編輯定義，請按一下 **設定原則** 動作。

![Edit][policies-edit]

原則定義是一份簡單的 XML 文件，描述一連串輸入和輸出陳述式。 可直接在定義視窗中編輯 XML。 右邊提供陳述式的清單，而適用於目前範圍的陳述式會啟用並反白顯示。如所示 **限制呼叫速率** 上方螢幕擷取畫面中的陳述式。

按一下已啟用的陳述式會在定義檢視中的游標位置上加入適當的 XML。 

原則陳述式的完整清單和其設定會提供 [原則參考文件][]。

例如，若要加入新的陳述式，以指定的 IP 位址限制連入要求，將游標置於的內容之內 `inbound` XML 項目按一下 **Restrict caller IPs** 陳述式。

![Restriction policies][policies-restrict]

這會將 XML 程式碼片段新增至提供設定陳述式之指引的 `inbound` 元素。

    <ip-filter action="allow | forbid">
        <address>address</address>
        <address-range from="address" to="address"/>
    </ip-filter>

若要限制輸入要求，只接受來自 IP 位址 1.2.3.4 的要求，請如下修改 XML：

    <ip-filter action="allow">
        <address>1.2.3.4</address>
    </ip-filter>

![Save][policies-save]

完成設定原則的陳述式時，按一下 [ **儲存** 和所做的變更會立即傳播至 API 管理閘道。

##<a name="sections"> </a>了解原則組態

原則是針對要求和回應而依序執行的一連串陳述式。 系統會將組態適當地劃分為 `inbound`、`backend`、`outbound` 和 `on-error` 區段，如下列組態所示。

    <policies>
      <inbound>
        <!-- statements to be applied to the request go here -->
      </inbound>
      <backend>
        <!-- statements to be applied before the request is forwarded to 
             the backend service go here -->
      </backend>
      <outbound>
        <!-- statements to be applied to the response go here -->
      </outbound>
      <on-error>
        <!-- statements to be applied if there is an error condition go here -->
      </on-error>
    </policies> 

若在處理要求期間發生錯誤，便會略過 `inbound`、`backend` 或 `outbound` 區段中的所有剩餘步驟，且執行程序會跳至 `on-error` 區段中的陳述式。 將原則陳述式置於 `on-error` 區段中，您即可使用 `context.LastError` 屬性檢閱錯誤、使用 `set-body` 原則檢查和自訂錯誤回應，以及設定錯誤發生時採取的動作。 會出現內建步驟的錯誤碼和處理原則陳述式期間可能會發生之錯誤的錯誤碼。 如需詳細資訊，請參閱 [API 管理原則中的錯誤處理](https://msdn.microsoft.com/library/azure/mt629506.aspx)。

由於可在不同層級指定原則可於 (全域、產品、API 和作業)，因此組態可讓您針對父原則，指定原則定義的陳述式執行順序。 

系統會以下列順序評估原則範圍。

1. 全域範圍
2. 產品範圍
3. API 範圍
4. 作業範圍

系統會根據 `base` 元素的位置 (若有的話)，評估其中的陳述式。

例如，若您在全域層級中有一個原則，還有一個為 API 設定的原則，則每次使用該特定 API 時，皆會套用這兩個原則。 API 管理可透過 base 元素來指定組合式原則陳述式的固定順序。 

    <policies>
        <inbound>
            <cross-domain />
            <base />
            <find-and-replace from="xyz" to="abc" />
        </inbound>
    </policies>

在上述的原則定義範例中，`cross-domain` 陳述式會在任何更高層級的原則執行之前執行，而這些原則後面又接著 `find-and-replace` 原則。

若原則陳述式中重覆出現相同的原則，將套用最近評估的原則。 您可以藉此覆寫在較高範圍定義的原則。 若要查看在原則編輯器中目前的範圍中的原則，請按一下 [ **重新計算選取範圍的有效原則**。

請注意：全域原則沒有父原則，因此在全域原則中使用 `<base>` 元素無法發揮任何作用。 

## 後續步驟

請查看有關原則運算式的下列影片。

> [AZURE.VIDEO policy-expressions-in-azure-api-management]

[Policy Reference]: api-management-policy-reference.md
[Product]: api-management-howto-add-products.md
[API]: api-management-howto-add-products.md#add-apis 
[Operation]: api-management-howto-add-operations.md

[Advanced policies]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[Control flow]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[Set variable]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[Policy expressions]: https://msdn.microsoft.com/library/azure/dn910913.aspx

[policies-menu]: ./media/api-management-howto-policies/api-management-policies-menu.png
[policies-editor]: ./media/api-management-howto-policies/api-management-policies-editor.png
[policies-scope]: ./media/api-management-howto-policies/api-management-policies-scope.png
[policies-configure]: ./media/api-management-howto-policies/api-management-policies-configure.png
[policies-edit]: ./media/api-management-howto-policies/api-management-policies-edit.png
[policies-restrict]: ./media/api-management-howto-policies/api-management-policies-restrict.png
[policies-save]: ./media/api-management-howto-policies/api-management-policies-save.png


