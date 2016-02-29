<properties
   pageTitle="定義 Azure 資源管理員範本中的相依性"
   description="說明如何在部署期間，將某個資源設定為相依於另一個資源。"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="mmercuri"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/07/2015"
   ms.author="mmercuri"/>

# 定義 Azure 資源管理員範本中的相依性

對於指定的資源，可以有多個上游相依性和子相依性，這些相依性是您的拓撲成功的關鍵。 您可以使用其他資源定義相依性 **dependsOn** 和 
**資源** 資源屬性。 相依性也可以指定使用 **參考** 函式。

    {
        "name": "<name-of-the-resource>",
        "type": "<resource-provider-namespace/resource-type-name>",
        "apiVersion": "<supported-api-version-of-resource>",
        "location": "<location-of-resource>",
        "tags": { <name-value-pairs-for-resource-tagging> },
        "dependsOn": [ <array-of-related-resource-names> ],
        "properties": { <settings-for-the-resource> },
        "resources": { <dependent-resources> }
    }

 也有可以定義資源之間關聯性的資源連結，以及定義資源群組中這些關聯性的支援。

## dependsOn

對於指定的虛擬機器，您可以依賴已經成功佈建的資料庫資源。 在其他情況下，您可能會相依的多個節點在叢集中安裝 
然後才能部署虛擬機器使用叢集管理工具。

在您的範本中，dependsOn 屬性可讓您為資源定義這個相依性。 它的值可以是資源名稱的逗號分隔清單。 之間的相依性 
評估資源，以及其相依順序部署資源。 資源若不互相依賴，則會嘗試平行部署資源。 

雖然您可能比較傾向於使用 dependsOn 對應您的資源之間的相依性，但是請務必了解為什麼您要這麼做，因為這可能會影響您部署的效能。 
例如，如果您這麼做是因為您想要記載資源互連的方式，則 dependsOn 並不是適當的方法。 DependsOn 的生命週期只能用於部署和是 
無法使用部署後。 一旦部署之後，就沒有任何方法可以查詢這些相依性。 使用 dependsOn 執行影響效能的風險，您可能會不小心分散 
使用平行處理原則，它可能會有部署引擎。 文件並提供查詢記載資源之間的關聯性，您應該改為使用 [連結資源](resource-group-link-resources.md)。

如果因為參考物件意味著資源的相依性而使用 reference 函式取得資源的表示法，則不需要這個元素。 事實上，如果沒有 
若要使用的參考和 dependsOn，本指南的選項是使用 reference 函式，並以隱含的參考。 再次強調，基本理由就是效能。  參考定義隱含相依性 
且已知是必要，因為它們在範本中參考。 存在表示它們彼此相關，避免再次最佳化效能，並避免潛在的風險 
轉移部署引擎轉為避免不必要的平行處理原則。

如果您需要定義某項資源和透過複製迴圈所建立之資源之間的相依性，可以將 dependsOn 項目設定為迴圈的名稱。 如需範例，請參閱 [Azure 資源管理員中建立資源的多個執行個體](resource-group-create-multiple.md)。

## 資源

resources 屬性可讓您指定與所定義的資源相關的子資源。 子資源僅能定義為 5 個層級的深度。 請務必注意，在子資源與父資源之間並不會建立隱含的相依性。 如果您需要在父資源之後部署子資源，您必須使用 dependsOn 屬性明確地敘述該相依性。 

每個父資源只接受特定的資源類型做為子資源。 中指定的可接受的資源類型 [範本結構描述](https://github.com/Azure/azure-resource-manager-schemas) 之父資源。 子資源類型的名稱包含父資源類型的名稱，例如 **Microsoft.Web/sites/config** 和 **Microsoft.Web/sites/extensions** 的這兩個子資源 **microsoft.web/sites**。

## reference 函式

reference 函式可讓運算式從其他 JSON 名稱和值組或執行階段資源衍生其值。 reference 運算式會隱含地宣告某個資源相依於另一個資源。 
所表示的屬性 **propertyPath** 下面是選擇性的如果未指定，參考是資源。

    reference('resourceName').propertyPath

您可以使用此元素或 dependsOn 元素指定相依性，但是您不需要針對相同的相依資源使用兩者。 本指南是以避免使用隱含參考 
風險不小心讓不必要的 dependsOn 元素防止部署引擎無法執行工作以平行方式部署的層面。

若要深入了解，請參閱 [參考函數](../resource-group-template-functions/#reference)。

## 後續步驟

- 若要了解如何建立 Azure 資源管理員範本，請參閱 [編寫範本](resource-group-authoring-templates.md)。 
- 如需在範本中可用的功能，請參閱 [樣板函式](resource-group-template-functions.md)。


