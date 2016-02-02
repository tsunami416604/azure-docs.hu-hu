<properties 
    pageTitle="如何在 App Service 環境中調整應用程式" 
    description="在 App Service 環境中調整應用程式" 
    services="app-service" 
    documentationCenter="" 
    authors="ccompy" 
    manager="stefsch" 
    editor="jimbe"/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/26/2015" 
    ms.author="ccompy"/>


# 在 App Service 環境中調整應用程式

基本上，App Service 環境是 Azure App Service 在 VNET 中的個人部署，僅可由您的訂用帳戶管理。 這類環境會提供新的網路功能，因為它們位於您的 VNET 中，而且還可以調整超過 Azure App Service 環境中通常可用的數量。 如果您需要更多有關哪些應用程式服務 (ase)，請查看 [什麼是 App Service 環境 ][whatisase]。 如需建立 App Service 環境，或在 App Service 環境中建立 web 應用程式的詳細資訊，請參閱 [如何建立 App Service 環境 ][howtocreatease] 和 [如何在 App Service 環境 ][createwebappinase]

快速提醒您，當您正常變更 web、 行動的小數位數屬性或在 Azure App Service API 應用程式時您要變更的應用程式服務 Plan(ASP) 層級。 如詳細調整應用程式服務計劃或只需 App Service 環境外部 App Service 方案的詳細資訊，請參閱 [調整 web 應用程式在 Azure App Service ][scalewebapp] 和 [App Service 方案深度概觀 ][appserviceplans]。

在 App Service 環境中調整應用程式是非常類似於一般調整應用程式。 在 Azure App Service 中，您通常有三件事可以調整：

- 定價方案
- 背景工作大小 (適用於專用的執行個體)
- 執行個體數目。

在 ASE 中不需要選取或變更定價方案。 就功能而言，它已經是 Premium 定價功能層級。 在 App Service 環境中也沒有任何共用的背景工作。 全部都是專用的背景工作。

對於背景工作大小 ASE 系統管理員可以指定要用於每個背景工作集區的計算資源大小。 這表示您可以有具 P4 計算資源的背景工作集區 1，以及具 P1 計算資源的背景工作集區 2 (如有需要的話)。 它們並沒有大小順序。 如需大小及其定價，請參閱以下文件 [Azure App Service 定價 ][appservicepricing]。 以下是 App Service 環境中的 Web 應用程式和 App Service 方案的調整選項：

- 背景工作集區選取
- 執行個體數目

變更任一項目是透過適當的 UI 顯示 ASE 裝載應用程式服務計劃。 您無法向上擴充您的 ASP 超過您的 ASP 是在背景工作集區中的可用計算資源數目。 如果您需要更多數量，則必須讓您的 ASE 系統管理員新增更多運算資源到您指定的背景工作集區。 如重新設定 ASE 的相關資訊，請參閱以下資訊: [如何設定 App Service 環境 ][howtoconfigurease]。 您也可能需利用 ASE 自動調整功能，以根據排程或計量增加容量。 若要取得更多詳細資料設定自動調整本身的 ASE 環境請參閱 [如何設定自動調整 App Service 環境 ][aseautoscale]。

![][1]

### 調整執行個體數目

當您在 App Service 環境中首次建立 Web 應用程式時，您應該相應增加為至少 2 個執行個體，才能提供容錯功能。

如果 ASE 有足夠的容量，這就很簡單。 您可移至具有您要相應增加之站台的 App Service 方案，然後選取 [調整]。 這會開啟 UI，您可以在當中為 ASP 手動設定調整或設定自動調整規則。 若要手動調整您的應用程式只需將 *** 藉由調整 *** 至 *** 手動輸入執行個體計數 ***。 從這裡拖曳滑桿至所需的數量，或將所需數量輸入滑桿旁邊的方塊。

![][2]

在 ASE 中的 ASP 自動調整規則與一般運作方式相同。 您可以選取 *** CPU 百分比 *** 下 *** 藉由調整 *** 建立 ASP 根據 CPU 百分比，或者您可以建立更複雜的規則，使用自動調整規則和 *** 排程和效能規則 ***。 若要查看更完整的詳細資料上設定自動調整使用本指南以下 [調整應用程式在 Azure App Service ][appscale]。


### 背景工作集區選取

如先前所述，背景工作集區選取是從 ASP UI 存取。 針對您想要調整，然後選取 [背景工作集區 ASP 開啟分頁。 您會看到您已在 App Service 環境中設定的所有背景工作集區。 如果您只有一個背景工作集區，您只會看到一個集區列出。 若要變更您的 ASP 處於何種背景工作集區，您只需選取您想要您的應用程式服務計劃 」，移至背景工作集區。

![][3]

將 ASP 從一個背景工作角色集區移到另一個集區之前，請務必確定您有足夠的容量可容納該 ASP。 在背景工作集區清單中，不只會列出背景工作集區名稱，您也可以查看該背景工作集區中有多少可用的背景工作。 請確定有足夠的執行個體可包含您的 App Service 方案。 如果您需要在背景工作集區中移入更多計算資雲，則必須讓您的 ASE 系統管理員加以新增。
> [AZURE.NOTE] 移動 ASP 從一個背景工作集區會導致該 ASP 應用程式重新啟動。 這會造成少量的停機時間，您的應用程式，根據您的應用程式重新啟動所需的時間。  

## 開始使用

若要開始使用 App Service 環境，請參閱 [如何建立 App Service 環境 ][howtocreatease]

如需 Azure App Service 平台的詳細資訊，請參閱 [Azure App Service ][azureappservice]。




[1]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-aspblade.png 
[2]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-manualscale.png 
[3]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-sizescale.png 
[whatisase]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/ 
[scalewebapp]: http://azure.microsoft.com/documentation/articles/web-sites-scale/ 
[howtocreatease]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/ 
[howtoconfigurease]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/ 
[createwebappinase]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-a-web-app-in-an-ase/ 
[appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/ 
[appservicepricing]: http://azure.microsoft.com/pricing/details/app-service/ 
[azureappservice]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/ 
[aseautoscale]: http://azure.microsoft.com/documentation/articles/app-service-environment-auto-scale/ 
[appscale]: http://azure.microsoft.com/documentation/articles/web-sites-scale/ 

