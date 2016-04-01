<properties
    pageTitle="登入 Windows Server VM | Microsoft Azure"
    description="了解如何使用 Azure 入口網站和資源管理員部署模型登入 Windows Server VM。"
    services="virtual-machines"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/15/2015"
    ms.author="cynthn"/>

# 如何登入執行 Windows Server 的虛擬機器 

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [傳統部署模型](virtual-machines-log-on-windows-server.md)。

您將使用 **連線** Azure 入口網站，以啟動遠端桌面工作階段中的按鈕。 首先您要連接至虛擬機器，然後登入。

## 連接至虛擬機器

1. 如果您尚未這樣做，請登入 [Azure 入口網站](https://portal.azure.com/)。

2.  在 [中樞] 功能表中，按一下 [ **瀏覽**。  

3.  在 [搜尋] 分頁中，向下捲動並按一下 [ **虛擬機器**。

    ![搜尋虛擬機器](./media/virtual-machines-log-on-windows-server-preview/search-blade-preview-portal.png)

4.  然後從清單中選取虛擬機器。

5. 在虛擬機器的分頁中，按一下 [ **連接**。

    ![連接至虛擬機器](./media/virtual-machines-log-on-windows-server-preview/preview-portal-connect.png)

## 登入虛擬機器

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## 疑難排解

如果登入的相關提示越幫越忙，或不是您的需要請參閱 [疑難排解遠端桌面連線至 windows Azure 虛擬機器](virtual-machines-troubleshoot-remote-desktop-connections.md)。 本文會逐步帶領您診斷及解決常見的問題。


