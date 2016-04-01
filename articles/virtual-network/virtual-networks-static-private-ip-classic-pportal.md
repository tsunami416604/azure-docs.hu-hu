<properties 
   pageTitle="如何在傳統模式中使用預覽入口網站設定靜態私人 IP| Microsoft Azure"
   description="了解靜態私人 IP 以及如何在傳統模式中使用入口網站進行管理"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn"
   tags="azure-service-management"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/11/2015"
   ms.author="telmos" />

# 如何在預覽入口網站中設定靜態私人 IP 位址 (傳統)

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)] 本文章涵蓋傳統部署模型。 您也可以 [管理資源管理員部署模型中的靜態私人 IP 位址](virtual-networks-static-private-ip-arm-pportal.md)。

[AZURE.INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

以下的範例步驟會預期已經建立簡單的環境。 如果您想要執行的步驟，因為它們會顯示在這份文件，先建立測試環境中所述 [建立 vnet](virtual-networks-create-vnet-classic-pportal.md)。

## 建立 VM 時如何指定靜態私人 IP 位址
若要建立名為的 VM *DNS01* 中 *前端* 名為 VNet 的子網路 *TestVNet* 固定的私人 ip *192.168.1.101*, ，依照下列步驟 ︰

1. 從瀏覽器，瀏覽至 http://portal.azure.com，並有必要，請登入您的 Azure 帳戶。
2. 按一下 [ **新增** > **計算** > **Windows Server 2012 R2 Datacenter**, ，請注意， **選取部署模型** 清單已顯示 **傳統**, ，然後按一下 [ **建立**。

    ![在預覽入口網站中建立 VM](./media/virtual-networks-static-ip-classic-pportal/figure01.png)

3. 在 **建立 VM** 刀鋒視窗中，輸入要建立的 vm 名稱 (*DNS01* 我們的案例中)，本機系統管理員帳戶和密碼。

    ![在預覽入口網站中建立 VM](./media/virtual-networks-static-ip-classic-pportal/figure02.png)

4. 按一下 [ **選擇性組態** > **網路** > **虛擬網路**, ，然後按一下 [ **TestVNet**。 如果 **TestVNet** 就無法使用，請確定您使用 *中部* 位置，而且已建立的這篇文章開頭所描述的測試環境。

    ![在預覽入口網站中建立 VM](./media/virtual-networks-static-ip-classic-pportal/figure03.png)

5. 在 **網路** 刀鋒視窗中，請確定目前選取的子網路是 *前端*, ，然後按一下 [ **IP 位址**, 下 **IP 位址指派** 按一下 **靜態**, ，然後輸入 *192.168.1.101* 的 **IP 位址** ，如下所示。

    ![在預覽入口網站中建立 VM](./media/virtual-networks-static-ip-classic-pportal/figure04.png) 

6. 按一下 **[確定]** 中 **IP 位址** 刀鋒視窗中，然後按一下 [ **[確定]** 中 **網路** 刀鋒視窗中，然後按一下 [ **確定** 中 **選擇性組態** 刀鋒視窗。
7. 在 **建立 VM** 刀鋒視窗中，按一下 [ **建立**。 請注意您儀表板中下方顯示的磚。

    ![在預覽入口網站中建立 VM](./media/virtual-networks-static-ip-classic-pportal/figure05.png)

## 如何擷取 VM 的靜態私人 IP 位址資訊

若要檢視使用上述步驟建立之 VM 的靜態私人 IP 位址資訊，請執行下列步驟。

1. 從 Azure 預覽入口網站中，按一下 [ **全部瀏覽** > **虛擬機器 （傳統）** > **DNS01** > **所有設定** > **IP 位址** ，並注意 IP 位址指派和 IP 位址，如下所示。

    ![在預覽入口網站中建立 VM](./media/virtual-networks-static-ip-classic-pportal/figure06.png)

## 如何移除 VM 的靜態私人 IP 位址
若要移除上方建立之 VM 的靜態私人 IP 位址，請遵循下列步驟。
    
1. 從 **IP 位址** ，如上所示的刀鋒視窗中按一下 **動態** 右邊 **IP 位址指派**, ，然後按一下 [ **儲存**, ，然後按一下 [ **是**。

    ![在預覽入口網站中建立 VM](./media/virtual-networks-static-ip-classic-pportal/figure07.png)

## 如何將靜態私人 IP 位址新增至現有的 VM
若要將靜態私人 IP 位址新增至使用上述步驟建立之 VM，請遵循下列步驟：

1. 從 **IP 位址** ，如上所示的刀鋒視窗中按一下 **靜態** 右邊 **IP 位址指派**。
2. 型別 *192.168.1.101* 的 **IP 位址**, ，然後按一下 [ **儲存**, ，然後按一下 [ **是**。

## 後續步驟

- 深入了解 [保留公用 IP](../virtual-networks-reserved-public-ip) 位址。
- 深入了解 [執行個體層級公用 IP (ILPIP)](../virtual-networks-instance-level-public-ip) 位址。
- 請參閱 [保留的 IP REST Api](https://msdn.microsoft.com/library/azure/dn722420.aspx)。

