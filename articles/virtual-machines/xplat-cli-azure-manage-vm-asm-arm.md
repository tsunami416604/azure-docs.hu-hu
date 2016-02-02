<properties
    pageTitle="適用於 VM 工作的對等 Azure CLI 命令 |Microsoft Azure"
    description="對等的 Azure CLI 命令，在 Azure 資源管理員和 Azure 服務管理模式中建立和管理 Azure VM"
    services="virtual-machines"
    documentationCenter=""
    authors="dlepow"
    manager="timlt"
    editor=""
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="command-line-interface"
    ms.workload="infrastructure-services"
    ms.date="08/28/2015"
    ms.author="danlep"/>



# 在使用適用於 Mac、Linux 及 Windows 的 Azure CLI 的 VM 工作中的對等資源管理員和服務管理命令

本文將說明在 Azure 服務管理和 Azure 資源管理員中建立和管理 Azure VM 的對等 Microsoft Azure 命令列介面 (Azure CLI) 命令。 將本文當成便利指南，來將指令碼從某一個命令模式移轉至另一個命令模式。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]



* 如果您尚未安裝 Azure CLI 及連線到您的訂閱，請參閱 [安裝 Azure CLI](../xplat-cli-install.md) 和 [從 Azure CLI 連接至 Azure 訂用帳戶](../xplat-cli-connect.md)。 當您想要使用資源管理員模式命令時，請務必使用登入方法連線。

* 若要開始使用 Azure CLI 並切換命令模式的資源管理員模式，請參閱 [搭配使用 Azure 命令列介面與資源管理員](xplat-cli-azure-resource-manager.md)。 根據預設，CLI 會在服務管理模式下啟動。 若要變更資源管理員模式，執行 `azure 組態模式 arm`。 若要返回服務管理模式，執行 `azure 組態模式 asm`。

* 如需線上命令說明和選項，輸入 `azure < 命令 >< 指令 >-說明` 或 `azure 說明 < 命令 >< 指令 >`。

## VM 工作

下表比較常見的 VM 工作，您可以在服務管理和資源管理員中使用 Azure CLI 命令來執行這類工作。 使用許多資源管理員命令時，您需要傳遞現有的資源群組名稱。
> [AZURE.NOTE] 這些範例不包括資源管理員中以範本為基礎的操作。 如需資訊，請參閱 [搭配使用 Azure 命令列介面與資源管理員](xplat-cli-azure-resource-manager.md)。

 工作| 服務管理| 資源管理員
-------------- | ----------- | -------------------------
 建立最基本的 VM| `azure vm 建立的 [選項] < dns->< 映像名稱 > [userName] [password]`| `azure vm 快速建立 [選項] < 資源群組 >< 名稱 >< 位置 >< os 類型 >< 映像的 urn >< 管理員的使用者名稱 >< 管理員密碼 >`<br/><br/>(取得 `映像 urn` 從 `azure vm 映像清單` 命令。)
 建立 Linux VM| `azure vm 建立的 [選項] < dns->< 映像名稱 > [userName] [password]`| `azure vm 建立的 [選項] < 資源群組 >< 名稱 >< 位置 > y 「 Linux 」`
 建立 Windows VM| `azure vm 建立的 [選項] < dns->< 映像名稱 > [userName] [password]`| `azure vm 建立的 [選項] < 資源群組 >< 名稱 >< 位置 >-y 「 Windows 」`
 列出 VM| `vm list [options]`| `vm list [options] < resource_group >`
 取得 VM 的相關資訊| `azure vm 顯示 [選項] < vm_name >`| `azure vm 顯示 [選項] < resource_group >< 名稱 >`
 啟動 VM| `azure vm start < 名稱 > [選項]`| `azure vm start < resource_group >< 名稱 > [選項]`
 停止 VM| `azure vm 關閉 [選項] < 名稱 >`| `azure vm 停止 < resource_group >< 名稱 > [選項]`
 解除配置 VM| 尚未提供| `azure vm 解除配置 < 資源群組 >< 名稱 > [選項]`
 重新啟動 VM| `azure vm 重新啟動 [選項] < vname >`| `azure vm 重新啟動 [選項] < resource_group >< 名稱 >`
 刪除 VM| `azure vm delete < 名稱 > [選項]`| `azure vm 中刪除 [選項] < resource_group >< 名稱 >`
 擷取 VM| `azure vm 擷取 < 名稱 > [選項]`| `azure vm 擷取 < resource_group >< 名稱 > [選項]`
 從使用者映像建立 VM| `azure vm 建立的 [選項] < dns->< 映像名稱 > [userName] [password]`| `azure vm 建立的 [選項] – q < 映像名稱 >< 資源群組 >< 名稱 >< 位置 >< os 類型 >`
 從特殊化磁碟建立 VM| `azure vm 建立的 [選項]-d < 自訂-資料檔 >< dns 名稱 > [userName] [password]`| `azue vm 建立的 [選項] – d < 作業系統的磁碟的 vhd >< 資源群組 >< 名稱 >< 位置 >< os 類型 >`
 將資料磁碟新增至 VM| `azure vm 磁碟附加 [選項] < vm 名稱 >< 磁碟映像的名稱 >` -或- <br/>  `vm 磁碟附加新 [選項] < vm 名稱 >< 大小-中-g b > [blob url]`| `azure vm 磁碟附加新 [選項] < 資源群組 >< vm 名稱 >< 大小-中-g b > [vhd 名稱]`
 從 VM 移除資料磁碟| `azure vm 磁碟中斷連結的 [選項] < vm 名稱 >< lun >`| `azure vm 磁碟中斷連結的 [選項] < 資源群組 >< vm 名稱 >< lun >`
 將泛型延伸模組新增至 VM| `azure vm 延伸模組設定 [選項] < vm 名稱 >< 擴充功能-名稱 >< 發行者名稱 >< 版本 >`| `azure vm 延伸模組設定 [選項] < 資源群組 >< vm 名稱 >< 名稱 >< 發行者->< 版本 >`
 將 VM 存取延伸模組新增至 VM| 尚未提供| `azure vm 重設存取 [選項] < 資源群組 >< 名稱 >`
 將 Docker 延伸模組新增至 VM| `azure vm docker 建立 [選項] < dns 名稱 >< 映像 >< 使用者名稱 > [密碼]`| `azure vm docker 建立 [選項] < 資源群組 >< 名稱 >< 位置 >< os 類型 >`
 將 Chef 延伸模組新增至 VM| `azure vm 延伸模組 get-chef < vm 名稱 > [選項]`| 尚未提供
 停用 VM 延伸模組| `azure vm 延伸模組設定 [選項] – b < vm 名稱 >< 擴充功能-名稱 >< 發行者名稱 >< 版本 >`| 尚未提供
 移除 VM 延伸模組| `azure vm 延伸模組設定 [選項]-u < vm 名稱 >< 擴充功能-名稱 >< 發行者名稱 >< 版本 >`| `azure vm 延伸模組設定 [選項]-u < 資源群組 >< vm 名稱 >< 名稱 >< 發行者->< 版本 >`
 列出 VM 延伸模組| `vm extension list [options]`| `azure vm 延伸模組取得 [選項] < 資源群組 >< vm 名稱 >`
 列出 VM 映像| `azure vm 映像清單 [選項]`| `vm image list [options] < 位置 >< 發行者 > [優惠] [sku]` -或- <br/> `azure vm 映像清單-發行者 [選項] < 位置 >` -或- <br/> `azure vm 映像清單提供 [選項] < 位置 >` OR- <br/> `azure vm 映像清單-sku < 位置 > [選項]`
 顯示 VM 映像| `azure vm 映像顯示 [選項]`| 尚未提供
 取得 VM 資源的使用量| 尚未提供| `azure 的 vm 清單使用量 < 位置 > [選項]`
 取得所有可用的 VM 大小| 尚未提供| `azure vm 大小，[選項]`


## 後續步驟

* 如需有關使用 Azure CLI 來使用資源管理員資源的詳細資訊，請參閱 [搭配使用 Azure 命令列介面與資源管理員](xplat-cli-azure-resource-manager.md) 和 [管理角色存取控制使用 Azure 命令列介面](../role-based-access-control-xplat-cli-install.md)。
* 如需 CLI 命令的其他範例，請參閱 [與 Azure 服務管理中使用 Azure 命令列介面](../virtual-machines-command-line-tools.md) 和
[使用 Azure CLI 與 Azure 資源管理員](azure-cli-arm-commands.md)。





