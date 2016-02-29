<properties
   pageTitle="資源管理員和服務管理 (傳統) 部署模式 | Azure"
   description="了解資源管理員與傳統部署模型之間的差異"
   services="virtual-network"
   documentationCenter=""
   authors="telmosampaio"
   manager="carolz"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/07/2015"
   ms.author="telmos"/>

# Azure 部署模型

Azure 平台正在轉換。  不論您是 Azure 新手或已使用它數年，務必了解我們在此轉換期間對此平台所做的一些重要變更。

所有 Azure 資源都支援下列一或兩個部署模型：

- **資源管理員:** 這是 Azure 資源的最新的部署模型。 大部分較新的資源都已支援這種部署模型，最後所有資源都會提供支援。   
 
- **傳統:** 這個模型現在支援大部分現有的 Azure 資源。 加入至 Azure 的新資源將不支援此模型。

每個 Azure 資源的文件會詳述可用以建立該資源的服務模型。

## 為何這有關係？ 

有所關係的原因如下︰

- 您在這兩種模型下使用的 Azure 平台功能會有所不同。  例如，使用資源管理員所建立的資源部署模型 (或只是資源管理員) 可以建立與 [Azure 資源管理員範本](resource-group-overview.md/#template-deployment), ，而不能搭配傳統部署模型所建立的資源。
- 個別 Azure 資源功能或行為在這兩種模型下會有所不同，或只存在於其中一種模型。  例如，負載平衡搭配傳統部署模型所建立的虛擬機器的流量是 *隱含* 因為虛擬機器是 Azure 雲端服務的成員，而且負載自動平衡雲端服務內的虛擬機器。 使用資源管理員所建立的虛擬機器不是雲端服務的成員，而且必須是不同的 Azure 負載平衡器資源 *明確* 建立負載平衡的流量分散到多個虛擬機器。  
- 在這兩種模型中，建立、設定及管理 Azure 資源的方式有所不同。
- 使用一種部署模型所建立的資源，不一定能與使用不同部署模型建立的資源交互操作。 例如，使用一種部署模型所建立的 Azure 虛擬機器只能連接至使用相同部署模型建立的 Azure 虛擬網路。    

每個部署模型的基礎就是每個資源的應用程式設計介面 (API)。  沒有 [資源管理員 API](https://msdn.microsoft.com/library/azure/dn948464.aspx) 資源管理員部署模型和 [服務管理 API](https://msdn.microsoft.com/library/azure/ee460799.aspx) 傳統部署模型。 開發人員可以撰寫程式碼使用這些 Api 互動 *直接*。  

IT 專業人員不過，通常與這些 Api 互動 *間接* 在網頁瀏覽器中使用圖形化的入口網站、 使用 Azure PowerShell cmdlet 的 Windows 電腦上，或在 Windows、 OS X 或 Linux 電腦上使用 Azure 命令列介面 (CLI)。 IT 專業人員所使用的這三種間接方法都會直接與 API 互動。 這表示 Azure 平台或資源引進新功能時，一律可先透過 API 直接取得，而在 API 可供使用後透過間接方法取得新資源和功能的支援。  

下列各節說明如何透過三種間接方法，使用不同的部署模型來設定 Azure 資源。

## 入口網站
Azure 有兩個入口網站︰

- **[Azure 入口網站](https://manage.windowsazure.com):** 了已使用 Azure 一段時間，如果您使用此入口網站。 它用來建立及設定可支援傳統部署模型的較舊 Azure 資源。 您無法使用它來建立或設定僅支援資源管理員的資源。 
- **[Azure 預覽入口網站](http://azure.microsoft.com/overview/preview-portal/):** 如果您使用較新的 Azure 資源，您可能使用此入口網站。 它可以用來建立及設定某些 Azure 資源。 您最終能使用它來建立和設定所有的 Azure 資源。 對於支援兩種部署模型的某些資源，此入口網站可用來建立及設定使用任何一種部署模型的資源。 

某些資源和功能只可以在其中一個入口網站中建立及設定。 某些資源或功能還不能在任一個入口網站中建立或設定，而只能透過 PowerShell、CLI 或兩者進行設定。 每個 Azure 資源的文件會詳述可用以建立該資源的方法。 

## PowerShell
使用 [PowerShell](powershell-install-configure.md) 您可以使用命令列或撰寫指令碼來建立及設定從 Windows 電腦的 Azure 資源。  個別的 Azure 資源都有 [資源管理員 cmdlet](https://msdn.microsoft.com/library/azure/mt125356.aspx), ，[服務管理 cmdlet](https://msdn.microsoft.com/library/azure/dn708504.aspx), ，或兩者。  某些資源和功能只可以在使用 PowerShell 或 CLI 加以建立及設定。 視資源而定，使用資源管理員 PowerShell Cmdlet 時，您可能會有兩個選項可供建立及設定 Azure 資源︰

- **PowerShell 指令程式僅:** 您可以建立及設定每個個別的每個資源使用的指令程式的 Azure 資源。 從命令列執行這項操作，或在 PowerShell 指令碼中包含您可儲存和設定版本的多個命令。

- **使用 Azure 資源管理員範本的 PowerShell cmdlet:** 您可以使用 PowerShell 建立 Azure 資源使用 Azure 資源管理員範本。 範本可予以儲存及設定版本。 深入閱讀 [應用程式使用 Azure 資源管理員範本部署](resource-group-template-deploy.md) 文件。 數個 [Azure 快速入門範本](http://azure.microsoft.com/documentation/templates/) 存在，可以下載和修改過的常見解決方案。

## CLI
您可以從使用 CLI 的 Windows、OS X 或 Linux 電腦建立及設定 Azure 資源。  讀取 [安裝 Azure CLI](xplat-cli-install.md) 文件，以您選擇的作業系統上安裝 CLI。 類似 PowerShell 中，有不同的命令，根據您建立的資源使用，必須使用 [資源管理員](xplat-cli-azure-resource-manager.md) 或 [傳統 (服務管理)](virtual-machines-command-line-tools.md) 部署模型。

## 後續步驟

- 深入了解 [資源管理員](/resource-group-overview.md)。
- 了解如何 [設計範本](/best-practices-resource-manager-design-templates.md)。
- 使用 [的最佳作法](/best-practices-resource-manager-examples.md)
