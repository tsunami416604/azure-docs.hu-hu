---
title: Hálózati biztonság az 1. generációs Azure Data Lake Storage-ben | Microsoft Docs
description: Ismerje meg, hogyan működik az IP-tűzfal és a virtuális hálózati integráció az 1. generációs Azure Data Lake Storage-ben
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/09/2018
ms.author: elsung
ms.openlocfilehash: 0da5962bc0b48a387ee82a1db36099682e14bca3
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/12/2018
ms.locfileid: "49168122"
---
# <a name="virtual-network-integration-for-azure-data-lake-storage-gen1---preview"></a>Virtuális hálózat integrációja az 1. generációs Azure Data Lake Storage-ben (előzetes verzió)

A virtuális hálózat integrációjának bemutatása az 1. generációs Azure Data Lake Storage-ben (előzetes verzió). A VNet-integráció segít az 1. generációs Azure Data Lake Storage-fiókokhoz való jogosulatlan hozzáférések megakadályozásában. Ennek érdekében a fiókokat a megadott virtuális hálózatokhoz és alhálózatokhoz köti. Mostantól az 1. generációs ADLS-fiókok úgy konfigurálhatók, hogy csak a kijelölt virtuális hálózatokról és alhálózatokról fogadjanak forgalmat, bármely más helyről pedig blokkolják a hozzáférést. Ez segíti az ADLS-fiók külső fenyegetések elleni védelmét.

Az 1. generációs ADLS-fiókok VNet-integrációja a virtuális hálózati szolgáltatási végpont biztonsági funkcióit használja a virtuális hálózat és az Azure Active Directory szolgáltatás között, hogy létrehozza a hozzáférési jogkivonat által tartalmazott további biztonsági jogcímeket. Ezután e jogcímek használatával hitelesíti a virtuális hálózatot az 1. generációs ADLS-fiókkal, és engedélyezi a hozzáférést.

> [!NOTE]
> Ez a technológia jelenleg előzetes verzióban van, így nem ajánljuk az éles környezetekben való használatát.
>
> A funkció felár nélkül használható. A fiók után az 1. generációs ADLS-fiókok standard díját ([díjszabás](https://azure.microsoft.com/pricing/details/data-lake-store/?cdn=disable)) és a használt Azure-szolgáltatások díjait ([díjszabás](https://azure.microsoft.com/pricing/#product-picker)) számítjuk fel.

## <a name="scenarios-for-vnet-integration-for-adls-gen1"></a>1. generációs ADLS-fiókok VNET-integrációjának forgatókönyvei

1. generációs ADLS-fiókok VNET-integrációjával az 1. generációs ADLS-fiókokhoz való hozzáférés a megjelölt virtuális hálózatokra és alhálózatokra korlátozható.  Ha a fiókot egy megadott virtuális hálózathoz/alhálózathoz köti, akkor más Azure-beli virtuális hálózatok vagy virtuális gépek nem férhetnek hozzá.  Funkcióit tekintve az 1. generációs ADLS-fiókok VNET-integrációja ugyanazt a fogatókönyvet valósítja meg, mint a [virtuális hálózati szolgáltatásvégpontok](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview).  Néhány fontos különbség van, amelyeket az alábbi szakaszok ismertetnek. 

![1. generációs ADLS-fiókok VNET-integrációjának forgatókönyv-diagramja](media/data-lake-store-network-security/scenario-diagram.png)

> [!NOTE]
> A helyszíni hálózatokról való hozzáférések engedélyezéséhez a meglévő IP-tűzfalszabályok is használhatók a VNet-szabályok mellett. 

## <a name="optimal-routing-with-adls-gen1-vnet-integration"></a>Optimális útválasztás az 1. generációs ADLS-fiókok VNET-integrációjával

A VNet-szolgáltatásvégpontok egyik legfőbb előnye a virtuális hálózat felőli [optimális útválasztás](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview#key-benefits).  Ahhoz, hogy az 1. generációs ADLS-fiókok felé irányuló útvonal-optimalizálás mindig azonos legyen, használja az alábbi [felhasználó által definiált útvonalakat](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined) a virtuális hálózat és az 1. generációs ADLS-fiók között:

- **ADLS nyilvános IP-címe** – Használja a nyilvános IP-címet az 1. generációs ADLS-célfiókokhoz.  Az 1. generációs ADLS-fiókok IP-címei a fiókok [DNS-nevének](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-connectivity-from-vnets#enabling-connectivity-to-azure-data-lake-storage-gen1-from-vms-with-restricted-connectivity) feloldásával azonosíthatók.  Hozzon létre külön bejegyzést mindegyik címhez.

```azurecli
# Create a Route table for your resource group
az network route-table create --resource-group $RgName --name $RouteTableName

# Create Route Table Rules for ADLS Public IP Addresses
# There will be one rule per ADLS Public IP Addresses 
az network route-table route create --name toADLSregion1 --resource-group $RgName --route-table-name $RouteTableName --address-prefix <ADLS Public IP Address> --next-hop-type Internet

# Update the VNet and apply the newly created Route Table to it
az network vnet subnet update --vnet-name $VnetName --name $SubnetName --resource-group $RgName --route-table $RouteTableName
```

## <a name="data-exfiltration-from-the-customer-vnet"></a>Adatok kiszűrése az ügyféloldali virtuális hálózatokról

Az ADLS-fiókok virtuális hálózatok felőli hozzáférésének biztosítása mellett érdemes gondoskodni arról is, hogy ne lehessen adatokat kiszűrni egy jogosulatlan fiókkal.

Javasoljuk, hogy a virtuális hálózaton használjon egy tűzfalmegoldást, amely megszűri a kimenő forgalmat a célfiók URL-címe alapján, és csak a jogosult 1. generációs ADLS-fiókok számára engedélyezi a hozzáférést.

Néhány elérhető lehetőség:
- [Azure Firewall](https://docs.microsoft.com/azure/firewall/overview): A virtuális hálózathoz [üzembe helyezhet és konfigurálhat egy Azure Firewallt](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal), amely biztosítja és az ismert és jogosult fiók-URL-címre korlátozza az ADLS kimenő forgalmát.
- [Hálózati virtuális berendezés (NVA)](https://azure.microsoft.com/solutions/network-appliances/) tűzfala: Ha a rendszergazda csak bizonyos kereskedelmi tűzfalkészítők termékeinek használatát engedélyezi, egy, az Azure Marketplace-en elérhető hálózati virtuális berendezési tűzfalmegoldás is elláthatja ugyanezt a funkciót.

> [!NOTE]
> Ha az adatok útvonalán egy tűzfalat használ, az egy további ugrást jelent az adatok útvonalán, amely hatással lehet a végpontok közötti adatátvitel teljesítményére, beleértve az elérhető adatátviteli sebességet és a kapcsolatok késését. 

## <a name="limitations"></a>Korlátozások
1.  A HDInsight-fürtöket újonnan kell létrehozni, amikor hozzáadjuk őket az előzetes verzióhoz.  Azokat a fürtöket, amelyek az 1. generációs ADLS VNet-integráció támogatásának bevezetése előtt lettek létrehozva, újra létre kell hozni, hogy támogassák ezt az új funkciót. 
2.  Ha egy új HDInsight-fürt létrehozásakor egy olyan 1. generációs ADLS-fiókot választ ki, amelyen engedélyezve van a VNET-integráció, akkor a folyamat sikertelen lesz. Először le kell tiltania a virtuális hálózati szabályt, vagy **engedélyeznie kell a hozzáférést minden hálózatról és szolgáltatásról** az ADLS-fiók **Tűzfal és virtuális hálózatok** panelén.  További információért lásd a [Kivételek](##Exceptions) szakaszt.
3.  Az 1. generációs ADLS VNET-integrációjának előzetes verziója nem használható az [Azure-erőforrások felügyelt identitásaival](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).  
4.  A virtuális hálózattal integrált 1. generációs ADLS-fiókokban található fájl- és mappaadatok nem érhetők el a portálról.  Ez magában foglalja a virtuális hálózathoz tartozó virtuális gépekről való hozzáférést és az olyan tevékenységeket is, mint a Data Explorer használata.  A fiókfelügyeleti tevékenységek továbbra is működni fognak.  A virtuális hálózattal integrált ADLS-fiókokban található fájl- és mappaadatok minden nem portálalapú erőforrás által elérhetők, beleértve az SDK-hozzáférést, a PowerShell-szkripteket és az egyéb (nem a portálról indított) Azure-szolgáltatásokat stb. 

## <a name="configuration"></a>Konfiguráció

### <a name="step1-configure-your-vnet-to-use-aad-service-endpoint"></a>1. lépés: Virtuális hálózat konfigurálása egy AAD-szolgáltatásvégpont használatára
1.  Nyissa meg az Azure Portalt, és jelentkezzen be a fiókjába. 
2.  [Hozzon létre egy új virtuális hálózatot](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) az előfizetésében, vagy nyisson meg egy meglévő virtuális hálózatot.  Jelenleg a virtuális hálózatnak és az 1. generációs ADLS-fióknak ugyanabban a régióban kell lennie. 
3.  A Virtuális hálózat panelen válassza a **Szolgáltatásvégpontok** lehetőséget. 
4.  Kattintson a **Hozzáadás** elemre egy új szolgáltatásvégpont hozzáadásához.
![VNet-szolgáltatásvégpont hozzáadása](media/data-lake-store-network-security/config-vnet-1.png)
5.  Válassza ki a **Microsoft.AzureActiveDirectory** szolgáltatást a végponthoz.
![A Microsoft.AzureActiveDirectory szolgáltatásvégpont kiválasztása](media/data-lake-store-network-security/config-vnet-2.png)
6.  Válassza ki, mely alhálózatok számára kívánja engedélyezni a csatlakozást, majd kattintson a **Hozzáadás** lehetőségre.
![Alhálózat kiválasztása](media/data-lake-store-network-security/config-vnet-3.png)
7.  A szolgáltatásvégpont hozzáadása akár 15 percet is igénybe vehet. A hozzáadást követően a szolgáltatásvégpont meg fog jelenni a listában. Ellenőrizze, hogy megjelenik-e, és hogy a beállítások megfelelően vannak-e konfigurálva. 
![A szolgáltatásvégpont sikeres hozzáadása](media/data-lake-store-network-security/config-vnet-4.png)

### <a name="step-2-set-up-the-allowed-vnetsubnet-for-your-adls-gen1-account"></a>2. lépés: Az 1. generációs ADLS-fiókhoz engedélyezett virtuális hálózat/alhálózat beállítása
1.  A virtuális hálózat konfigurálását követően [hozzon létre egy új 1. generációs Azure Data Lake Storage-fiókot](data-lake-store-get-started-portal.md#create-a-data-lake-storage-gen1-account) az előfizetésében, vagy nyisson meg egy meglévő 1. generációs ADLS-fiókot. Jelenleg az 1. generációs ADLS-fióknak és a virtuális hálózatnak ugyanabban a régióban kell lennie. 
2.  Válassza a **Tűzfalak és virtuális hálózatok** elemet.

  > [!NOTE]
  > Ha a **Tűzfalak és virtuális hálózatok** elem nem látható a beállítások között, jelentkezzen ki a portálról. Zárja be a böngészőt. Ürítse ki a böngésző gyorsítótárát. Indítsa újra a számítógépet, és próbálkozzon újra.

  ![Virtuális hálózati szabály hozzáadása az ADLS-fiókhoz](media/data-lake-store-network-security/config-adls-1.png)
3.  Válassza a **Kijelölt hálózatok** lehetőséget. 
4.  Kattintson a **Meglévő virtuális hálózat hozzáadása** elemre.
  ![Meglévő virtuális hálózat hozzáadása](media/data-lake-store-network-security/config-adls-2.png)
5.  Válassza ki azokat a virtuális hálózatokat és alhálózatokat, amelyek számára engedélyezi a csatlakozást, majd kattintson a **Hozzáadás** elemre.
  ![A virtuális hálózat és az alhálózatok kiválasztása](media/data-lake-store-network-security/config-adls-3.png)
6.  Győződjön meg arról, hogy a virtuális hálózatok és alhálózatok megfelelően jelennek meg a listában, majd kattintson a **Mentés** gombra.
  ![Az új szabály mentése](media/data-lake-store-network-security/config-adls-4.png)

  > [!NOTE]
  > A beállítások módosításának életbe léptetése akár 5 percig is eltarthat.

7.  [Opcionális] Ha a virtuális hálózatok és alhálózatok mellett konkrét IP-címekről is engedélyezni kívánja a csatlakozást, azt ugyanennek az oldalnak a **Tűzfal** szakaszában állíthatja be. 

## <a name="exceptions"></a>Kivételek
A **Tűzfal és virtuális hálózatok** panel Kivételek területén két jelölőnégyzet található, amelyekkel engedélyezhető a csatlakozás bizonyos Azure-beli szolgáltatásokból és virtuális gépekről.
![A Tűzfal és virtuális hálózatok kivételei](media/data-lake-store-network-security/firewall-exceptions.png)
- A **Hozzáférés engedélyezése ehhez a Data Lake Storage Gen1-fiókhoz minden Azure-szolgáltatásból** beállítás minden Azure-szolgáltatás (pl. Azure Data Factory, Event Hubs, minden Azure-beli virtuális gép stb.) számára engedélyezi az ADLS-fiókkal való kommunikációt.

- A **Hozzáférés engedélyezése ehhez a Data Lake Storage Gen1-fiókhoz az Azure Data Lake Analytics számára** beállítás engedélyezi az Azure Data Lake Analytics számára, hogy csatlakozzon az ADLS-fiókhoz. 

Javasoljuk, hogy ezeket a kivételeket tartsa kikapcsolva, és csak akkor kapcsolja be, ha e szolgáltatások számára a virtuális hálózaton kívülről is muszáj biztosítania a csatlakozási lehetőséget.
