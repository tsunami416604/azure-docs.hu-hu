---
title: "Az Azure leválasztott telepítési döntések Azure verem integrált rendszerek |} Microsoft Docs"
description: "Határozza meg a központi telepítési tervének kidolgozásához többcsomópontos Azure verem Azure kapcsolódó központi telepítések."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: afea4b23aca322321b4096cdfdc9d30b087a2f30
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="azure-disconnected-deployment-planning-decisions-for-azure-stack-integrated-systems"></a>Az Azure-kapcsolat nélküli telepítés tervezési megfontolások Azure verem integrált rendszerek
Után úgy döntött, [hogyan fogja integrálja Azure verem a hibrid felhőkörnyezet](azure-stack-deployment-decisions.md), majd véglegesítése is az Azure-verem telepítési döntések meghozatalában.

A leválasztott az Azure-telepítés lehetőséget, üzembe helyezés és használat Azure verem az internetkapcsolat nélkül. Azonban az kapcsolat nélküli telepítést, azonban legfeljebb egy AD FS identitás tárolására és a kapacitás-alapú számlázási modellt. 

Válassza ezt a beállítást, ha Ön:
- Kell biztonsági, illetve egyéb korlátozások igénylő Azure verem telepíthet olyan környezetben, amely nem kapcsolódik az internethez.
- Szeretné tiltani adatokkal (ideértve a használati adatok) az Azure-ba történő elküldését.
- Kizárólag a magánfelhő-megoldások, amelyek a vállalati intranethez telepítve van, és nem szeretné a hibrid környezetek, Azure verem használni szeretne.

> [!TIP]
> Egyes esetekben az ilyen típusú környezet is nevezzük "Tengeralattjáró forgatókönyv".

A leválasztott központi telepítés nem feltétlenül jelenti, hogy nem később csatlakozni az Azure-verem példányának Azure hibrid bérlői Virtuálisgép-forgatókönyvek. Azt jelenti, hogy nincs kapcsolat az Azure-bA üzembe helyezése során, vagy nem kívánja az Azure Active Directoryt az identitás-tárolóként. Ha azt szeretné, hogy a telepítést, függetlenül a identitás tárolására használni kívánt Azure kapcsolódik a csatlakozás az Azure rendszerbe állítási beállítást kell kiválasztani. 

## <a name="features-that-are-impaired-or-unavailable-in-disconnected-deployments"></a>Korlátozott vagy nem érhető el a leválasztott környezetekhez funkciókra 
Azure verem legjobban, ha a kapcsolódó Azure-ba, ezért fontos megjegyezni, hogy nincsenek-e bizonyos szolgáltatásait és funkcióit, amelyek korlátozott, vagy teljesen nem érhető el, a kapcsolat nélküli módban készült. 

|Szolgáltatás|Kapcsolat nélküli módban gyakorolt hatás|
|-----|-----|
|A Virtuálisgép-telepítéshez DSC-bővítménnyel konfigurálása a feladás egy vagy több Virtuálisgép-telepítéshez|Sérült - DSC-bővítményt keresi az internethez a WMF legújabb Verziójára.|
|A Virtuálisgép-telepítéshez Docker kiterjesztésű Docker parancsok futtatásához|Sérült – Docker ellenőrzi az interneten található legújabb verzióhoz, és ez az ellenőrzés sikertelen lesz.|
|Az Azure-verem portál dokumentációjában hivatkozások|Nem érhető el – hivatkozások például visszajelzés segítségre van szüksége, gyors üzembe helyezés, az internetes URL-címe nem fognak működni használó stb.|
|Riasztási szervizelési/megoldás, amely egy online szervizelési útmutató hivatkozik|Nem érhető el – bármely riasztási eltávolításának hivatkozásokat tartalmaz, hogy használja az internetes URL-címe nem fognak működni.|
|Piactér szindikálási – válassza ki, és adja hozzá a gyűjtemény csomagokat közvetlenül az Azure piactérről|Nem érhető el – a szolgáltatás működéséhez Azure és az Azure Active Directory-fiókkal való kapcsolódást.|
|Az Azure-verem központi telepítésének kezelése az Azure Active Directory összevonási fiókok segítségével|Nem érhető el – ez a funkció használatához Azure hozzáférés szükséges. Az AD FS, ha a helyi Active Directory példánya használandók.|
|Például a webalkalmazás és az SQL erőforrás-szolgáltató|Nem érhető el – például a webalkalmazás és az SQL erőforrás-szolgáltatók internetelérés szükséges a tartalomhoz.|
|Parancssori felület (CLI)|Sérült – CLI lecsökkentette és szolgáltatás alapelveinek létesítésének funkciókat.|
|A Visual Studio – a Cloud discovery|Sérült – a Cloud Discovery vagy deríti fel másik felhőt, vagy egyáltalán nem működik.|
|A Visual Studio – Active Directory összevonási szolgáltatások|Sérült – csak a Visual Studio Enterprise támogatja: AD FS.
Telemetria|Nem érhető el – és minden függő telemetriai adatok külső gyűjtemény csomagként Azure verem Telemetriai adatokat.|
|Tanúsítványok|Nem érhető el – internetkapcsolat szükség a visszavont tanúsítványok listáját (CRL), és Online tanúsítvány állapota protokoll (OSCP) szolgáltatások HTTPS környezetében.|
|Key-Vault|Sérült – Key vault gyakori használati eset az, hogy az alkalmazás megnyithassa a futási időben. Ennek az alkalmazást kell egy egyszerű szolgáltatást a címtárban. Az Azure Active Directoryban a normál felhasználók (nem rendszergazda) rendszer az új szolgáltatásnevekről alapértelmezés szerint. Az Active Directory (AD FS használatával) nem válnak. Ez helyezi a küszöbértéket a végpont élmény mert egyik mindig haladjon végig a directory-rendszergazda bármely alkalmazás hozzáadása.| 

## <a name="learn-more"></a>Részletek
- További információ a használati esetek, megvásárlásáról, partnerek és OEM hardverszállítók: a [Azure verem](https://azure.microsoft.com/overview/azure-stack/) termék oldalát.
- A terv és a földrajzi rendelkezésre állása információt Azure verem integrált rendszerek, tekintse meg a: [Azure verem: Azure kiterjesztése](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/). 
- További információt a Microsoft Azure verem csomagolás és árképzési [töltse le a .pdf](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf). 

## <a name="next-steps"></a>További lépések
[Datacenter hálózati integráció](azure-stack-network.md)