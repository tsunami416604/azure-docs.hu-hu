---
title: Virtuálisgép-lemezkép létrehozása az Azure Marketplace a műszaki előfeltételei |} A Microsoft Docs
description: Ismerje meg a követelményeknek, létrehozásának és üzembe helyezése virtuálisgép-lemezkép megvásárlásához mások számára az Azure piactéren.
services: marketplace-publishing
documentationcenter: ''
author: HannibalSII
manager: hascipio
editor: ''
ms.assetid: 63c16966-0304-4b17-a715-368a0a5ccb2c
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 04/29/2016
ms.author: hascipio; v-divte
ms.openlocfilehash: 79fb9869b37e82df3f41a50e4425e7c0cd08c841
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51255268"
---
# <a name="technical-prerequisites-for-creating-a-virtual-machine-image-for-the-azure-marketplace"></a>Virtuálisgép-lemezkép létrehozása az Azure Marketplace a műszaki előfeltételei
A folyamat megkezdése előtt figyelmesen olvassa el, és megismerheti, hogy hol és miért érdemes minden egyes lépést. Amennyire csak lehetséges, meg kell a vállalati adatok és egyéb adatok előkészítése, töltse le a szükséges eszközök, illetve műszaki összetevőket hozhat létre a ajánlat létrehozásának megkezdése előtt. Ezeket az elemeket, ez a cikk áttekintésével egyértelműen meg kell határozni.  

## <a name="download-needed-tools--applications"></a>Töltse le a szükséges eszközök és alkalmazások
Készen áll a megkezdése előtt a következőkkel kell rendelkeznie:

* Operációs rendszerétől függően céloz meg, telepítse a [Azure PowerShell-parancsmagok](https://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/WindowsAzurePowershellGet.3f.3f.3fnew.appids) vagy [Linux parancssori felületi eszközének](https://go.microsoft.com/fwlink/?LinkId=253472&clcid=0x409) származó a [Azure letöltések](https://azure.microsoft.com/downloads/) lapot.
* Telepítse az Azure Storage Explorert a Codeplex Tárházportálról.
* Töltse le, és a minősítési vizsgálati eszköz telepítése az Azure Certified:
  * [http://go.microsoft.com/fwlink/?LinkID=526913](https://go.microsoft.com/fwlink/?LinkID=526913). A minősítőeszköz futtatását Windows-alapú számítógépre van szüksége. Ha nem rendelkezik elérhető Windows-alapú számítógép, futtathatja az eszközt a Windows-alapú virtuális gép az Azure-ban.

## <a name="platforms-supported"></a>Támogatott platformok
Az Azure-alapú virtuális gépek Windows vagy Linux rendszeren is fejleszthet. Bizonyos elemek a közzétételi folyamat – például létrehozhat egy Azure-kompatibilis virtuális merevlemezről (VHD) – használja a különböző eszközök és operációs rendszerétől függően használja lépéseket:  

* Ha Linux használ, tekintse át a "Hozzon létre egy Azure-kompatibilis virtuális Merevlemezt (Linux-alapú)" részt a a [közzétételi útmutató virtuálisgép-lemezkép](marketplace-publishing-vm-image-creation.md).
* Ha Windows használ, tekintse át a "Hozzon létre egy Azure-kompatibilis virtuális Merevlemezt (Windows-alapú)" részt a a [közzétételi útmutató virtuálisgép-lemezkép](marketplace-publishing-vm-image-creation.md).

> [!NOTE]
> Egy Windows-alapú gépre való hozzáférésre van szüksége:
> 
> * A minősítőeszköz érvényesítési futtassa.
> * A virtuális merevlemez hitelesítő elküldése a megosztott virtuális merevlemez hozzáférési jogosultságkód URL-Címének létrehozása.
> 
> 

## <a name="develop-your-vhd"></a>Fejlessze virtuális Lemezképét
Azure virtuális merevlemezeket a felhőalapú vagy helyszíni fejleszthet:

* Felhőalapú fejlesztési azt jelenti, hogy minden fejlesztési lépést mennek végbe távolról rezidens Azure-beli virtuális Merevlemezt.
* A helyszíni fejlesztési van szükség a virtuális merevlemez letöltésére, valamint a helyszíni infrastruktúra fejlesztése. Bár ez nem lehetséges, ezt nem javasoljuk. Vegye figyelembe, hogy fejleszti a Windows- vagy SQL helyszíni szükséges hozzá, a helyszíni megfelelő licenckulcsot. Nem tartalmazza, és az SQL Server telepítése egy virtuális gép létrehozása után. Az ajánlat is a jóváhagyott SQL-lemezképet az Azure Portalon kell alapozni. Ha úgy dönt, hogy a helyszíni fejlesztés, eltérően Ha Ön is a felhőben való fejlesztés néhány lépést kell végrehajtania. A megfelelő információkat [egy a helyszíni virtuális gép rendszerképének létrehozása](marketplace-publishing-vm-image-creation-on-premise.md).

[link-acct-creation]:marketplace-publishing-accounts-creation-registration.md
