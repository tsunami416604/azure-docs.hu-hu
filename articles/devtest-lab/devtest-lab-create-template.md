    <properties
    pageTitle="Create VM templates | Microsoft Azure"
    description="Learn how to create VM templates from VHD images"
    services="devtest-lab,virtual-machines"
    documentationCenter="na"
    authors="tomarcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="devtest-lab"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/01/2015"
    ms.author="tarcher"/>

# 建立 VM 範本

## 概觀

一旦 [建立實驗室](devtest-lab-create-lab.md), ，您可以 [將 Vm 加入至該實驗室](devtest-lab-add-vm-with-artifacts.md) 從 VM 範本的清單。 在本文中，您將了解如何上傳和設定虛擬硬碟 (VHD) 映像檔，以做為可用來建立您 VM 的範本。 如果您不熟悉的 VHD 映像，請參閱 [建立並上傳至 Azure 的 Windows Server VHD](../virtual-machines-create-upload-vhd-windows-server.md) 文件，以了解如何建立 VHD 映像。 一旦建立或取得 VHD 映像的存取權之後，本文將逐步帶領您上傳映像，並從中建立範本。

## 建立 VM 範本

1. 登入 [Azure 預覽入口網站](http://portal.azure.com)。

1. 點選 **瀏覽**, ，然後點選 [ **DevTest 實驗室** 從清單中。

1. 從實驗室清單中，點選所需的實驗室。  

1. 在 [實驗室] 分頁中，點選 [ **設定**。

    ![實驗室設定](./media/devtest-lab-create-template/lab-blade-settings.png)

1. 實驗室 **設定** 刀鋒視窗中，點選 **範本**。

    ![範本選項](./media/devtest-lab-create-template/lab-blade-settings-templates.png)

1. 在 **範本** 刀鋒視窗中，點選 **+ 範本**。

    ![加入範本](./media/devtest-lab-create-template/add-template.png)

1. 在 **新增範本** 刀鋒視窗中:

    1. 輸入範本的名稱。 這個名稱會在建立新的 VM 時顯示於範本清單中。

    1. 輸入範本的描述。 這個描述會在建立新的 VM 時顯示於範本清單中。

    1. 點選 **映像**。

    1. 如果未列出您要的映像，而且您想要將它加入，請跳至 [加入新的範本映像](#add-a-new-template-image) 區段，然後回到這裡完成。

    1. 選取所需的映像。

    1. 點選 **確定** 關閉 **新增範本** 刀鋒視窗。

1. 點選 **作業系統組態**。

1. 在 **作業系統組態** 索引標籤上，選取 [ **Windows** 或 **Linux**。

1. 如果 **Windows** 是透過核取方塊，選取此選項，指定是否 *Sysprep* 已在電腦上執行。

1. 輸入 **使用者名稱** 機器。

1. 輸入 **密碼** 機器。 **注意:** 密碼會以純文字格式顯示。

1. 點選 **確定** 關閉 **作業系統組態** 刀鋒視窗。

1. 指定 **位置**。

1. 點選 **確定** 來建立範本。

##加入新的範本映像

若要加入新的範本映像，您必須具有 VHD 映像檔案的存取權。

1. 在 **新增範本映像** 刀鋒視窗中，點選 **上傳映像使用 PowerShell**。

    ![上傳映像](./media/devtest-lab-create-template/upload-image-using-psh.png)

1. 下一個刀鋒視窗將顯示修改和執行 PowerShell 指令碼的指示，該指令碼會將 VHD 映像檔案上傳到您的 Azure 訂用帳戶。 **注意:** 此程序可能會很長的映像檔和您的連線速度的大小而定。

##後續步驟

一旦您加入使用 VM 範本建立 VM 時，下一步是 [將 VM 加入至 DevTest 實驗室](devtest-lab-add-vm-with-artifacts)。

