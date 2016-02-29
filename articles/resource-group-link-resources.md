<properties 
    pageTitle="在「Azure 資源管理員」中連結資源" 
    description="在「Azure 資源管理員」中建立不同資源群組中的資源之間的連結。" 
    services="azure-resource-manager" 
    documentationCenter="" 
    authors="tfitzmac" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="azure-resource-manager" 
    ms.workload="multiple" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/06/2015" 
    ms.author="tomfitz"/>

# 在「Azure 資源管理員」中連結資源

部署後，您可能想要查詢資源之間的關聯性或連結。 相依性會通知部署，但是， 
在部署時結束生命週期。 一旦部署完成，相依資源之間就沒有可識別的關聯性。

相反地，Azure 資源管理員提供新功能，稱為資源來建立及查詢的關聯性連結 
之間的資源。 您可以判斷哪些資源連結至某資源，或哪些資源是連結自某資源。 

資源連結的領域可以是訂用帳戶、資源群組或特定資源。 這表示該資源的連結可以記錄 
跨越多個資源群組的關聯性。 當您開始將您的方案分解成多個範本和多個資源群組， 
有一種機制識別這些資源的連結，確實可運用。 比方說，它是通用的資料庫中有自己的生命週期 
位於其中一個資源群組，且具有不同生命週期的應用程式位在不同的資源群組。 應用程式，連接至資料庫 
在不同的資源群組資源之間沒有連結。 

所有已連結的資源都必須屬於同一個訂用帳戶。 每個資源都可以連結至其他 50 個資源。 如果有任何連結的資源 
刪除或移動，連結擁有者必須清除剩餘的連結。

## 範本中的連結

若要定義在範本中的資源之間的連結，請參閱 [資源連結的範本結構描述](resource-manager-template-links.md)。

## 使用 REST API 連結

若要定義已部署資源之間的連結，請執行：

    PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/{provider-namespace}/{resource-type}/{resource-name}/providers/Microsoft.Resources/links/{link-name}?api-version={api-version}

將 {subscription-id} 取代成您的訂用帳戶識別碼。 取代 {資源群組、} {提供者命名空間、 {resource-type}，和 {resource-name} 值， 
識別連結中的第一個資源。 以要建立之連結的名稱取代 {link-name}。 對於 api-version，請使用  2015-01-01。

在要求中，包含定義連結中第二個資源的物件：

    {
        "name": "{new-link-name}",
        "properties": {
            "targetId": "subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/{provider-namespace}/{resource-type}/{resource-name}/",
            "notes": "{link-description}",
        }
    }

properties 元素包含第二個資源的識別碼。

如需詳細資訊，包括如何抓取連結的相關資訊，請參閱 [連結的資源](https://msdn.microsoft.com/library/azure/mt238499.aspx)。

## 後續步驟

- 您也可以使用標記來組織您的資源。 若要深入了解標記資源，請參閱 [使用標記來組織您的資源](resource-group-using-tags.md)。
- 如需如何建立範本，並定義要部署資源的說明，請參閱 [編寫範本](resource-group-authoring-templates.md)。

