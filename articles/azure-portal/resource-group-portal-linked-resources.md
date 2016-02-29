<properties 
    pageTitle="磚庫中相關的資源與連結的資源" 
    description="了解 Azure Preview 入口網站磚庫中的相關資源和連結資源。" 
    services="azure-portal" 
    documentationCenter="" 
    authors="adamabdelhamed" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="azure-portal" 
    ms.workload="multiple" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/16/2015" 
    ms.author="adamab"/>

# 磚庫中相關的資源與連結的資源

磚庫可讓您尋找磚的特定資源，並將其拖曳到您目前的刀鋒視窗。 
您可以使用磚庫建立跨越資源的管理檢視。 
對於任何指定的資源，相關的資源包括所有共用相同的資源 
資源群組做為資源，以及連結至或從資源的任何資源。

## Azure 資源管理員中連結的資源

連結是 Azure 資源管理員的一項功能。  它可讓您宣告之間的關聯性 
即使它們不是存放在相同的資源群組中的資源。 連結不會影響對執行階段 
您的資源，不會影響帳單和以角色為基礎的存取不會影響效能。  它是只是您可以一種機制 
用來表示關聯性，使磚庫等工具可以提供豐富的管理 
體驗。  您的工具可以檢查使用連結 API 的連結，並提供自訂的關聯性 
管理以及體驗。 

## 如何連結我的資源？

當您建立資源透過入口網站，或藉由部署範本，以透過 Azure PowerShell 或 Azure CLI 時，連結是 
自動建立某些相依的資源。 您可以使用，以也以程式設計方式連結資源 
[連結資源 REST API](https://msdn.microsoft.com/library/azure/mt238499.aspx) 或藉由宣告範本中的關聯性。 
使用連結的資源的完整討論，請參閱 [Azure 資源管理員中連結資源](../resource-group-link-resources.md)。

## 後續步驟

- 如果您需要撰寫 Azure 資源管理員範本的簡介，請參閱 [編寫範本](../resource-group-authoring-templates.md)。
- 若要深入了解更詳細的資源之間建立連結，請參閱 [Azure 資源管理員中連結資源](../resource-group-link-resources.md)。
- 若要了解有關使用透過預覽入口網站的資源群組的詳細資訊，請參閱 [使用 Azure Preview 入口網站來管理您的 Azure 資源](resource-group-portal.md)。

