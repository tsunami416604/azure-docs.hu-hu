---
title: "Az Azure csatlakoztatott telepítési döntések Azure verem integrált rendszerek |} Microsoft Docs"
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
ms.date: 02/06/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: def9d5381144026b5ad0e8a076edd3c0692a08f4
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="azure-connected-deployment-planning-decisions-for-azure-stack-integrated-systems"></a>Az Azure-csatlakoztatott telepítés tervezési megfontolások Azure verem integrált rendszerek
Után úgy döntött, [hogyan fogja integrálja Azure verem a hibrid felhőkörnyezet](azure-stack-connection-models.md), majd véglegesítése is az Azure-verem telepítési döntések meghozatalában.

Kapcsolódik az Azure rendszerbe állítása Azure verem azt jelenti, hogy is vagy az Azure Active Directory (Azure AD), vagy az Active Directory összevonási szolgáltatások (AD FS) a identitás tárolójához. Is választhat vagy számlázási modellt: fizetési,-akkor-használható vagy kapacitás-alapú. A csatlakoztatott központi telepítés oka az, az alapértelmezett beállítás lehetővé teszi az ügyfelek a legtöbb érték elfogyott Azure, különösen lekérése az Azure és az Azure-vermet érintő hibrid felhős rendszerekben. 

## <a name="choose-an-identity-store"></a>Válassza ki az identitás tárolására
Egy csatlakoztatott üzembe helyezéssel választhat Azure AD vagy AD FS-hez a identitás tárolására. A kapcsolat nélküli telepítést, internetkapcsolat nélküli csak használható az AD FS.

Az identitás tároló választásra nincs hatással a bérlői virtuális gépek (VM). Bérlői virtuális gépek is dönthet, mely a csatlakozás attól függően, hogy hogyan kell konfigurálni kívánt identitás tárolására: az Azure AD, a Windows Server Active Directory-tartományhoz, munkacsoporthoz, stb. Ez az az Azure verem identity provider döntés független. 

Például ha központi telepítése az infrastruktúra-szolgáltatási bérlői virtuális gépek Azure-verem tetején található, és szeretné, hogy a vállalati Active Directory-tartományhoz csatlakoztatja, és ott fiókok használata, továbbra is ehhez. Nem kell az Azure AD identity tároló itt használja ezeket a fiókokat.

### <a name="azure-ad-identity-store"></a>Az Azure AD identity tároló
Ha inkább az Azure AD a identitás tárolására van szükség, két Azure AD-fiókok: egy globális rendszergazdai fiókot és számlázási fiókot. Ezek a fiókok a azonos, és különböző fiókok lehet. Amíg ugyanazzal a fiókkal használatával lehet egyszerűbb és hasznos, ha korlátozott számú Azure-fiókra, az üzleti igényeknek megfelelően mérete alapján feltételezhető két fiók használata:

1. **Globális rendszergazdai fiókkal** (csak a kapcsolódó központi telepítések elvégzéséhez szükséges). Ez az Azure-fiókot, amellyel alkalmazások és az infrastruktúra-szolgáltatásokat Azure verem szolgáltatásnevekről létrehozása az Azure Active Directoryban. Ennek a fióknak rendelkeznie kell ahhoz a könyvtárhoz, az Azure-verem rendszer lesz telepítve, a címtár rendszergazdai jogosultságokkal. A "felhő üzemeltetője" válik az Azure AD globális rendszergazda bérlői és fogja használni: 
    - Kiépítés és delegált alkalmazásokhoz és az összes Azure verem szolgáltatás együttműködhet az Azure Active Directory és a Graph API igénylő szolgáltatásnevekről. 
    - A szolgáltatás-rendszergazda fiókot. Ez az az alapértelmezett szolgáltató előfizetés (amely később módosítható) tulajdonosa. Ezzel a fiókkal az Azure-verem felügyeleti portál be tud jelentkezni, és ajánlatokat és tervek létrehozása, kvótáit és más felügyeleti feladatokat Azure verem használható.
2. **Számlázási partner** (szükséges is csatlakoztathatók, illetve központi telepítések). Az Azure-fiókot az Azure-verem integrált rendszert és az Azure kereskedelmi háttér számlázási kapcsolatának létrehozására szolgál. Ez az a fiók, amely az Azure-verem díjak alapján számlázzuk. Ez a fiók lesz a piactér szindikálási és egyéb hibrid környezetekben is. 

### <a name="ad-fs-identity-store"></a>AD FS identitás tárolására
Válassza ezt a beállítást, ha a szolgáltatás-rendszergazdai fiókok, például a vállalati Active Directory, a saját identitás tárolására használni kívánt.  

## <a name="choose-a-billing-model"></a>Válassza ki a számlázási modelljét
Ön választhatja **fizetési,-akkor-használható** vagy a **kapacitás** számlázási modellt. Fizetési,-akkor-használható számlázási modell központi telepítések legalább 30 naponta Azure kapcsolaton keresztül a jelentés-használatra képesnek kell lennie. A fizetési,-akkor-használható számlázási modellt ezért csak csatlakoztatott központi telepítéseknél használható.  

### <a name="pay-as-you-use"></a>Fizetési,-akkor-használható
A fizetési,-akkor-használható számlázási modellt használata Azure-előfizetéshez számítjuk fel. Csak kell fizetnie az Azure-verem szolgáltatások használatakor. Ha ez a modell mellett dönt, akkor az Azure-előfizetés és az adott előfizetéshez tartozó Fiókazonosító (például serviceadmin@contoso.onmicrosoft.com). EA, a CSP és az CSL előfizetések használata támogatott. Használati jelentésben konfigurálja [Azure verem regisztrációs](azure-stack-registration.md).

> [!NOTE]
> A legtöbb esetben vállalati ügyfelek EA előfizetések fogja használni, és szolgáltatók CSP vagy CSL előfizetések fogja használni.

Ha szeretne egy CSP-előfizetés használatára, mely CSP előfizetés használatához azonosításához, mivel a megfelelő módszer függ a pontos CSP forgatókönyv az alábbi táblázatban tekintheti át:

|Eset|Tartomány és az előfizetés beállításai|
|-----|-----|
|Ön egy **közvetlen CSP Partner** vagy egy **közvetett CSP-szolgáltató**, és az Azure-verem fog működni|Egy CSL (közös szolgáltatás réteg)-előfizetés használatára.<br>     vagy<br>Hozzon létre egy leíró nevet a Partner Centerben az Azure AD-bérlő. Például &lt;a szervezet > CSPAdmin egy Azure CSP-előfizetéshez társítva.|
|Ön egy **közvetett CSP viszonteladóhoz**, és az Azure-verem fog működni|Kérje meg a közvetett CSP-szolgáltató a szervezet Azure AD-bérlő létrehozása egy Azure CSP-előfizetése társítva van Partnerközpontjában használatával.|

### <a name="capacity-based-billing"></a>A kapacitás alapján számlázási
Ha úgy dönt, hogy a kapacitás számlázási modellt használja, meg kell vásárolnia egy Azure verem kapacitásának megtervezése SKU a kapacitás, a rendszer alapján. Meg kell tudni, hogy a helyes mennyiség megvásárlásához a Azure verem fizikai magok száma. 

A kapacitás számlázási szükséges egy nagyvállalati szerződés (EA) Azure-előfizetés regisztrációjához. A hiba oka, hogy a regisztrációs beállítja az együttműködési, amelyhez az Azure-előfizetés szükséges. Az előfizetés Azure verem használata nem szolgál.

## <a name="learn-more"></a>További információ
- További információ a használati esetek, megvásárlásáról, partnerek és OEM hardverszállítók: a [Azure verem](https://azure.microsoft.com/overview/azure-stack/) termék oldalát.
- A terv és a földrajzi rendelkezésre állása információt Azure verem integrált rendszerek, tekintse meg a: [Azure verem: Azure kiterjesztése](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/). 
- További információt a Microsoft Azure verem csomagolás és árképzési [töltse le a .pdf](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf). 

## <a name="next-steps"></a>További lépések
[Datacenter hálózati integráció](azure-stack-network.md)