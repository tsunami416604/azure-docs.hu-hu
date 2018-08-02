---
title: Az Azure leválasztott telepítési döntések meghozatalában az Azure Stack integrált rendszerek |} A Microsoft Docs
description: Tervezési megfontolások a több csomópontos, az Azure Stack Azure-kapcsolattal rendelkező központi telepítések központi telepítési határozza meg.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: 4574b140e2e17462a5ff696b913bb4ef7bcb0ad0
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39412756"
---
# <a name="azure-disconnected-deployment-planning-decisions-for-azure-stack-integrated-systems"></a>Az Azure leválasztott telepítés tervezési megfontolások az Azure Stack integrált rendszerek
Miután döntött [hogyan fogja integrálja az Azure Stack a hibridfelhő-környezet](azure-stack-connection-models.md), majd az Azure Stack telepítési döntések is véglegesítése.

A leválasztott az Azure üzembe helyezési lehetőséget, üzembe helyezése és használata az Azure Stack az internethez való csatlakozás nélkül. Azonban egy kapcsolat nélküli telepítés pedig csak az AD FS ügyfélidentitás-tárolóval, és a kapacitás-alapú számlázási modell. 

Válassza ezt a beállítást, ha Ön:
- Biztonsági vagy egyéb korlátozásokat, amelyek megkövetelik olyan környezetben, amely nem csatlakozik az internethez az Azure Stack üzembe helyezése rendelkezik.
- Szeretné blokkolni data (használati adatok is beleértve) az Azure-ba történő elküldését.
- Szeretné használni az Azure Stack csak, egy magánfelhő-alapú megoldás, amely telepíti a vállalati intranethez, és nem érdeklik a hibrid környezetek.

> [!TIP]
> Egyes esetekben az ilyen típusú környezet is nevezik "tenger forgatókönyv".

Egy kapcsolat nélküli üzembe helyezés nem feltétlenül jelenti azt, hogy nem tudja később fogja összekapcsolni az Azure Stack-példány az Azure hibrid bérlői Virtuálisgép-forgatókönyvekhez. Az azt jelenti, hogy nincs kapcsolat az Azure-ban üzembe helyezés során, vagy nem szeretné használni az Azure Active Directory az ügyfélidentitás-tárolóval.

## <a name="features-that-are-impaired-or-unavailable-in-disconnected-deployments"></a>Az funkciók, amelyek korlátozott vagy nem érhető el a leválasztott környezetekhez 
Az Azure Stack való csatlakozáskor az Azure-ba, ezért fontos megjegyezni, hogy egyes szolgáltatások és funkciók, amelyek korlátozott, vagy teljesen nem érhető el, a kapcsolat nélküli üzemmódban működnek a legjobban úgy lett kialakítva. 

|Szolgáltatás|Kapcsolat nélküli módban gyakorolt hatás|
|-----|-----|
|A DSC-bővítmény használatával konfigurálja a virtuális gép üzembe helyezés után a virtuális gép üzembe helyezése|Sérült - DSC bővítmény keres az internethez a WMF legújabb Verziójára.|
|Virtuális gép üzembe helyezése a Docker-parancsok futtatásához Docker-bővítmény|Sérült – Docker keresi a legújabb verziót az internethez, és ez az ellenőrzés sikertelen lesz.|
|Az Azure Stack portálon dokumentációra mutató hivatkozások|Nem érhető el – hivatkozások mint visszajelzés segítségre van szüksége, a rövid útmutatóban egy internetes URL-cím nem fognak működni használják stb.|
|Riasztási szervizelési/megoldás, amely hivatkozik egy online szervizelési útmutató|Nem érhető el – bármely riasztás szervizelése hivatkozások használó internetes URL-cím nem fog működni.|
|Marketplace – válassza ki, és adja hozzá a katalógus-csomagokat közvetlenül az Azure Marketplace-ről|Sérült – Azure Stack (nélkül bármely internetkapcsolat) kapcsolat nélküli üzemmódban történő telepítésekor Piactéri elemek nem lehet letölteni az Azure Stack-portál használatával. Használhatja azonban a [marketplace tartalomtípus-gyűjtési eszköz](https://docs.microsoft.com/azure/azure-stack/azure-stack-download-azure-marketplace-item#download-marketplace-items-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity) töltse le a marketplace-elemek olyan gépre, amely rendelkezik internetkapcsolattal, és utána az Azure Stack környezettel.|
|Azure Active Directory összevonási fiókok használata kezelheti az Azure Stack üzemelő példányához|Nem érhető el – ez a funkció kapcsolódnia kell az Azure-bA. Ehelyett az AD FS egy helyi Active Directory-példányt kell használni.|
|App Services|Sérült - WebApps kérhet Internet-hozzáférés frissített tartalom.|
|Parancssori felület (CLI)|Sérült – parancssori felület csökkentette a funkciót, hitelesítési és a kiépítési szolgáltatás alapelvek tekintetében.|
|A Visual Studio – a Cloud discovery|Sérült – a Cloud Discovery vagy fogja felderíteni a különböző felhőkben, vagy egyáltalán nem működnek.|
|A Visual Studio – Active Directory összevonási szolgáltatások|Sérült – csak a Visual Studio Enterprise támogatja az AD FS.
Telemetria|Nem érhető el – Telemetriai adatokat az Azure Stack, és minden külső katalóguscsomagok, amely telemetriai adatokat függ.|
|Tanúsítványok|Nem érhető el – az internetkapcsolat szükség a visszavont tanúsítványok listája (CRL), és Online tanúsítvány állapotának protokoll (OSCP) szolgáltatások HTTPS kontextusában.|
|Key-Vault|Sérült – Key vault gyakori használati eset az, hogy egy alkalmazás futásidőben titkos kódok olvasását. Ennek az alkalmazásnak kell egy egyszerű szolgáltatást a címtárban. Az Azure Active Directoryban hozzáadható a szolgáltatásnevek alapértelmezés szerint, normál felhasználók (nem rendszergazda jogosultságú). Az Active Directory (AD FS használatával) nincsenek. Ez egy küszöbértéket a helyezi a teljes körű felhasználói élményt, mert az egyik mindig haladjon végig a directory-rendszergazda bármely alkalmazás hozzáadása.| 

## <a name="learn-more"></a>Részletek
- További információ a használati esetek, vásárlás, partnerek és OEM hardverszállítók: a [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) termékoldalán.
- Az ütemterv és a rendelkezésre állás földrajzi információ az Azure Stack integrált rendszerek, tekintse meg a: [Azure Stack: Azure bővítménye](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/). 
- A Microsoft Azure Stack csomagolás és a díjszabással kapcsolatos további [töltse le a .pdf](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf). 

## <a name="next-steps"></a>További lépések
[Adatközpont hálózati integráció](azure-stack-network.md)