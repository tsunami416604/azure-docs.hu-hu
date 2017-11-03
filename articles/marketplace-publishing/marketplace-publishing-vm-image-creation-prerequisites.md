---
title: "Hozzon létre egy virtuálisgép-lemezkép az Azure piactéren műszaki előfeltételei |} Microsoft Docs"
description: "A létrehozása és telepítése a virtuálisgép-lemezkép megvásárlásához mások az Azure piactéren vonatkozó követelmények megértése érdekében."
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 63c16966-0304-4b17-a715-368a0a5ccb2c
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 04/29/2016
ms.author: hascipio; v-divte
ms.openlocfilehash: af3e2ad623d8d7bfafe676411f9ae3fbee78aab8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="technical-prerequisites-for-creating-a-virtual-machine-image-for-the-azure-marketplace"></a>Hozzon létre egy virtuálisgép-lemezkép az Azure piactéren műszaki előfeltételei
A folyamat megkezdése előtt alaposan és megértettem, hogy hol és miért minden egyes lépést. Amennyire csak lehetséges, meg kell készítse elő a vállalati adatok és egyéb adatokat, töltse le a szükséges eszközök, és/vagy technikai összetevő létrehozása az ajánlat létrehozási folyamat megkezdése előtt. Ezek az elemek egyértelműen kiderül, hogy ez a cikk áttekintése kell lennie.  

## <a name="download-needed-tools--applications"></a>Töltse le a szükséges eszközök és alkalmazások
Készen áll a megkezdése előtt a következő elemeket kell rendelkezniük:

* Attól függően, hogy milyen operációs rendszert céloz meg, telepítse a [Azure PowerShell-parancsmagok](https://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/WindowsAzurePowershellGet.3f.3f.3fnew.appids) vagy [Linux parancssori felület eszköz](https://go.microsoft.com/fwlink/?LinkId=253472&clcid=0x409) a a [Azure letölti](https://azure.microsoft.com/downloads/) lap.
* Telepítse az Azure Storage Explorer a Codeplex webhelyen.
* Töltse le, és a hitelesítésszolgáltató vizsgálati eszköz telepítése az Azure hitelesített:
  * [http://go.microsoft.com/fwlink/?LinkId=526913](http://go.microsoft.com/fwlink/?LinkID=526913). A hitelesítésszolgáltató eszköz futtatásához Windows-alapú számítógépre van szüksége. Ha nem rendelkezik egy Windows-alapú számítógép elérhető, futtathatja az eszközt a Windows-alapú virtuális gépek használata az Azure-ban.

## <a name="platforms-supported"></a>Támogatott platformok
Virtuális gépek Azure-alapú Windows vagy Linux fejleszthet. A közzétételi folyamat – például létrehozhat egy Azure-kompatibilis virtuális merevlemez (VHD) – használja a különböző eszközök és lépések attól függően, hogy milyen operációs rendszert használ egyes elemei:  

* Ha Linux használ, tekintse át a "Hozzon létre egy Azure-kompatibilis VHD-t (Linux-alapú)" részt a a [virtuális gép lemezképének közzétételi útmutató](marketplace-publishing-vm-image-creation.md).
* Ha Windows használ, tekintse át a "Hozzon létre egy Azure-kompatibilis VHD-t (Windows-alapú)" részt a a [virtuális gép lemezképének közzétételi útmutató](marketplace-publishing-vm-image-creation.md).

> [!NOTE]
> A Windows-alapú gép elérésére lesz szüksége:
> 
> * A hitelesítésszolgáltató érvényesítési eszköz futtatásához.
> * Hozzon létre a virtuális merevlemez hitelesítő küldése a megosztott virtuális merevlemez hozzáférési aláírás URL-CÍMÉT.
> 
> 

## <a name="develop-your-vhd"></a>A virtuális merevlemez fejlesztése
Azure virtuális merevlemezek, a felhőben, vagy a helyszíni fejleszthet:

* Felhőalapú fejlesztési azt jelenti, hogy minden fejlesztési lépést távolról elvégzett rezidens Azure virtuális Merevlemezt.
* A helyi fejlesztési letöltése a virtuális merevlemez és a fájlrendszersérülések segítségével helyszíni infrastruktúra van szükség. Bár ez lehetséges, nem ajánlott. Vegye figyelembe, hogy fejleszti a Windows vagy SQL helyszíni szükséges hozzá a megfelelő helyszíni licenckulcsot. Nem tartalmaznak, vagy az SQL Server telepítése a virtuális gép létrehozása után. Az ajánlat is jóváhagyott SQL lemezkép az Azure portálról kell alapozni. Ha úgy dönt, hogy a helyszíni fejlesztése, néhány lépést működnek, mint ha volt fejlesztése a felhőben kell elvégeznie. A releváns információt [hozzon létre egy helyszíni Virtuálisgép-lemezkép](marketplace-publishing-vm-image-creation-on-premise.md).

[link-acct-creation]:marketplace-publishing-accounts-creation-registration.md
