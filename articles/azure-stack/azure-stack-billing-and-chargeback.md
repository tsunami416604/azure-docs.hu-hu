---
title: Ügyfél számlázás és költséghelyi elszámolás az Azure Stackben |} A Microsoft Docs
description: Ismerje meg, hogyan kérheti le az erőforrás-használati adatokat az Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: brenduns
ms.reviewer: alfredop
ms.openlocfilehash: f055837711b52fc32cb387fb86c623d3502f47ab
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2018
ms.locfileid: "39090139"
---
# <a name="usage-and-billing-in-azure-stack"></a>Használat és számlázás az Azure Stackben

Ez a cikk bemutatja, hogyan Azure Stack felhasználói erőforrás-használat számlázzuk ki. Megtudhatja, hogyan érhető el a számlázási adatokat analytics és a díj vissza.

Az Azure Stack gyűjt, és a csoportok használt erőforrások használati adatait. Ezután az Azure Stack az Azure kereskedelmi továbbítja ezeket az adatokat. Az Azure kereskedelmi verziójánál díjat Azure Stack használatának ugyanúgy mint volna fel díjat az Azure-használat.

Használati adatok és az exportálás, hogy a saját számlázási vagy költséghelyi elszámolási adapter használatával biztonsági másolatot a rendszer, vagy exportálhatja, és a egy üzleti intelligencia eszköz, például a Microsoft Power BI is kaphat.


## <a name="usage-pipeline"></a>Használati folyamat

Mindegyik erőforrás-szolgáltató az Azure Stackben közzéteszi a használati adatok erőforrás-használat. Rendszeres időközönként a Használatmérő szolgáltatást (óránként, és naponta) összesíti használati adatait, és a használati adatbázisban tárolja azokat. Az Azure Stack operátorai és felhasználói hozzáférhet a tárolt adatok az Azure Stack Resource használati API-kon keresztül. 

Ha rendelkezik [regisztrálva az Azure Stack-példány az Azure-ral](azure-stack-register.md), az Azure Stack a használati adatok küldése az Azure kereskedelmi van konfigurálva. Az adatok feltöltése az Azure-ba, után is férhet hozzá a számlázási portálon keresztül vagy az Azure erőforrás-használati API-k használatával. Tekintse meg a [használati adatok jelentése](azure-stack-usage-reporting.md) a cikkből megtudhatja, hogy több milyen használatával kapcsolatos adatok jelentése az Azure-bA.  

Az alábbi képen látható a használati folyamat a legfontosabb összetevők: 

![Használati folyamat](media\azure-stack-billing-and-chargeback\usagepipeline.png)

## <a name="what-usage-information-can-i-find-and-how"></a>Milyen használati adatokat is megkeresheti, és hogyan?

Az Azure Stack-erőforrás-szolgáltatók (például számítási, tárolási és hálózati), az egyes előfizetésekhez óránkénti időközönként használati adatok létrehozásához. A használati adatokat az erőforrás, például az erőforrás nevét, a használt előfizetés és a felhasznált mennyiség használt információkat tartalmazza. A mérőszámok azonosító erőforrások kapcsolatos további információkért tekintse meg a [használati API – gyakori kérdések](azure-stack-usage-related-faq.md) cikk.

A használati adatok begyűjtését követően van [jelentett az Azure-bA](azure-stack-usage-reporting.md) számla, amely az Azure számlázási portálján tekinthetők létrehozásához. 

> [!NOTE]  
> Használati adatok jelentése, nem szükséges, az Azure Stack Development Kit és a kapacitás modellben licenc Azure Stackkel integrált rendszer felhasználók. Az Azure Stackben licenceléssel kapcsolatos további tudnivalókért tekintse meg a [formátumokat támogató csomagolási és díjszabás](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf) Adatlap.

Az Azure számlázási portálján felszámítható erőforrásokat használati adatait jeleníti meg. Mellett a felszámítható erőforrásokat az Azure Stack szélesebb készletét használja erőforrásokat, amely az Azure Stack környezettel REST API-k vagy a Powershellen keresztül érhető el a használati adatokat rögzíti. Az Azure Stack-operátorok a használati adatokat kaphat az összes felhasználói előfizetések. Egyéni felhasználók számára csak kaphat használati adatait. 

## <a name="usage-reporting-for-multitenant-cloud-service-providers"></a>Jelentéskészítés a több-bérlős Felhőszolgáltatók használat

Egy több-bérlős Felhőszolgáltató (CSP) rendelkező Azure Stack használatával számos ügyfél érdemes külön-külön mindegyik ügyfél használati jelentést, hogy a szolgáltató az Azure-előfizetések használati díjat. 

Minden egyes ügyfél-azonosítót fog kérni egy másik Azure Active Directory (Azure AD) bérlő által képviselt lesz. Az Azure Stack egy CSP előfizetés hozzárendelése minden egyes Azure AD-bérlő támogatja. Az alapszintű Azure Stack-regisztráció a bérlők és azok az előfizetések adhat hozzá. Az alap regisztrációját, készen áll az összes Azure-t. Ha egy előfizetést a bérlő nincs regisztrálva, a felhasználó továbbra is használhatja az Azure Stack, és használatuk küld a kiinduló regisztráció használt előfizetés. 


## <a name="next-steps"></a>További lépések

[Regisztráljon az Azure Stack használatával](azure-stack-registration.md)

[Az Azure-bA az Azure Stack-használati adatok jelentése](azure-stack-usage-reporting.md)

[Szolgáltatói erőforrás-használati API](azure-stack-provider-resource-api.md)

[Bérlői erőforrás-használati API](azure-stack-tenant-resource-usage-api.md)

[Használattal kapcsolatos gyakori kérdések](azure-stack-usage-related-faq.md)