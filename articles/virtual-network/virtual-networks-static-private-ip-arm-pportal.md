<properties 
   pageTitle="如何在 ARM 模式中使用預覽入口網站設定靜態私人 IP| Microsoft Azure"
   description="了解靜態 IP (DIP) 如何在 ARM 模式中使用預覽入口網站管理它們"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/11/2015"
   ms.author="telmos" />

# 如何在預覽入口網站中設定靜態私人 IP 位址

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-arm-include](../../includes/virtual-networks-static-private-ip-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)] 本文涵蓋了資源管理員部署模型。 您也可以 [管理傳統部署模型中的靜態私人 IP 位址](virtual-networks-static-private-ip-classic-pportal.md)。

[AZURE.INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

以下的範例步驟會預期已經建立簡單的環境。 如果您想要執行的步驟，因為它們會顯示在這份文件，先建立測試環境中所述 [建立 vnet](virtual-networks-create-vnet-arm-pportal.md)。

## 如何建立用來測試靜態私人 IP 位址的 VM

您無法在資源管理員部署模式中建立 VM 期間，使用預覽入口網站設定靜態私人 IP 位址。 您必須先建立 VM，才能將其私人 IP 設定為靜態。

若要建立名為的 VM *DNS01* 中 *前端* 名為 VNet 的子網路 *TestVNet*, ，依照下列步驟。

1. 從瀏覽器，瀏覽至 http://portal.azure.com，並有必要，請登入您的 Azure 帳戶。
2. 按一下 [ **新增** > **計算** > **Windows Server 2012 R2 Datacenter**, ，請注意， **選取部署模型** 清單已顯示 **資源管理員**, ，然後按一下 [ **建立**, ，如下圖所示。

    ![在預覽入口網站中建立 VM](./media/virtual-networks-static-ip-arm-pportal/figure01.png)

3. 在 **基本概念** 刀鋒視窗中，輸入要建立的 vm 名稱 (*DNS01* 我們的案例中)，本機系統管理員帳戶和密碼，如下圖所示。

    ![基本概念刀鋒視窗](./media/virtual-networks-static-ip-arm-pportal/figure02.png)

4. 請確定 **位置** 選取 *中部*, ，然後按一下 [ **選取現有** 下 **資源群組**, ，然後按一下 [ **資源群組** 一次，然後按一下 [ *TestRG*, ，然後按一下 [ **確定**。

    ![基本概念刀鋒視窗](./media/virtual-networks-static-ip-arm-pportal/figure03.png)

5. 在 **大小選擇 「** 分頁中，選取 **A1 標準**, ，然後按一下 [ **選取**。

    ![選擇尺寸刀鋒視窗](./media/virtual-networks-static-ip-arm-pportal/figure04.png) 

6. 在 **設定** 刀鋒視窗中，請確定已設定下列屬性會以下列的值來設定，然後按一下 [ **確定**。

    -**儲存體帳戶**: *vnetstorage*
    - **網路**: *TestVNet*
    - **子網路**: *前端*

    ![選擇尺寸刀鋒視窗](./media/virtual-networks-static-ip-arm-pportal/figure05.png)  

7. 在 **摘要** 刀鋒視窗中，按一下 [ **確定**。 請注意您儀表板中下方顯示的磚。

    ![在預覽入口網站中建立 VM](./media/virtual-networks-static-ip-arm-pportal/figure06.png)

## 如何擷取 VM 的靜態私人 IP 位址資訊

若要檢視使用上述步驟建立之 VM 的靜態私人 IP 位址資訊，請執行下列步驟。

1. 從 Azure 預覽入口網站中，按一下 [ **全部瀏覽** > **虛擬機器** > **DNS01** > **所有設定** > **網路介面** 然後按一下 [只有列出的網路介面上。

    ![部署 VM 磚](./media/virtual-networks-static-ip-arm-pportal/figure07.png)

2. 在 **網路介面** 刀鋒視窗中，按一下 [ **所有設定** > **IP 位址** ，並注意 **指派** 和 **IP 位址** 值。

    ![部署 VM 磚](./media/virtual-networks-static-ip-arm-pportal/figure08.png)

## 如何將靜態私人 IP 位址新增至現有的 VM
若要將靜態私人 IP 位址新增至使用上述步驟建立之 VM，請遵循下列步驟：

1. 從 **IP 位址** ，如上所示的刀鋒視窗中按一下 **靜態** 下 **指派**。
2. 型別 *192.168.1.101* 的 **IP 位址**, ，然後按一下 [ **儲存**。

    ![在預覽入口網站中建立 VM](./media/virtual-networks-static-ip-arm-pportal/figure09.png)

>[AZURE.NOTE] 如果在按一下 **儲存** 您會注意到，作業仍會設定為 **動態**, ，這表示您所輸入的 IP 位址已在使用。 請嘗試不同的 IP 位址。

## 如何移除 VM 的靜態私人 IP 位址
若要移除上方建立之 VM 的靜態私人 IP 位址，請遵循下列步驟。
    
1. 從 **IP 位址** ，如上所示的刀鋒視窗中按一下 **動態** 下 **指派**, ，然後按一下 [ **儲存**。

## 後續步驟

- 深入了解 [保留公用 IP](../virtual-networks-reserved-public-ip) 位址。
- 深入了解 [執行個體層級公用 IP (ILPIP)](../virtual-networks-instance-level-public-ip) 位址。
- 請參閱 [保留的 IP REST Api](https://msdn.microsoft.com/library/azure/dn722420.aspx)。
