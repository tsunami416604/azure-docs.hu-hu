---
title: "Ügyfél számlázási és az Azure-készletben jóváírási |} Microsoft Docs"
description: "Ismerje meg, hogyan lehet lekérni az erőforrás-használati adatait az Azure oszlopból."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: eca335797f48b7c44351655f17c8b6499f3d5999
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2018
---
# <a name="usage-and-billing-in-azure-stack"></a>Használati és számlázási Azure verem

Ez a cikk ismerteti, hogyan Azure verem felhasználók számlázása az erőforrás-használat. Megtudhatja, hogyan érhető el a számlázási adatokat elemzés és vissza kell fizetni.

Azure verem gyűjt és használati adatok használt összes erőforrás csoportosítja, és továbbítja ezeket az adatokat az Azure kereskedelmi. Az Azure kereskedelmi váltók stb meg Azure verem használati azonos módon, akkor volna kiszámlázni Önnek Azure használatra.

Használati adatok és az Exportálás úgy, hogy a saját számlázással vagy az ingyenesen elérhető biztonsági másolatot a rendszer a számlázási adapter használatával vagy exportálni onnan üzleti intelligencia eszközhöz hasonló Microsoft Power bi-ban, és használni analytics is beszerezheti.


## <a name="usage-pipeline"></a>Használati folyamat

Mindegyik erőforrás-szolgáltató Azure verem bocsát ki a használati adatok egy erőforrás-használat. Rendszeres időközönként a Usage webszolgáltatás (óránkénti és naponta) összesíti használati adatait, és a használati adatbázisban tárolja. Az Azure verem operátorok és felhasználók használhatják a tárolt adatok az Azure verem Erőforrás kihasználtsága API-k segítségével. 

Ha rendelkezik [Azure verem példány regisztrálva van az Azure](azure-stack-register.md), Azure verem a használati adatok küldése az Azure kereskedelmi van konfigurálva. Az adatok feltöltése az Azure-ba, után hozzá tud férni a számlázási portálon keresztül, vagy az Azure erőforrás-használati API-k használatával. Tekintse meg a [használati adatok jelentése](azure-stack-usage-reporting.md) témakörét, ahol több milyen használatával kapcsolatos adatok bejelentések Azure.  

A következő kép bemutatja a legfontosabb összetevők, a használati feldolgozási: 

![Használati folyamat](media\azure-stack-billing-and-chargeback\usagepipeline.png)

## <a name="what-usage-information-can-i-find-and-how"></a>Milyen használati információk találhatók, és hogyan?

Az Azure verem erőforrás-szolgáltatók, köztük a számítási, tárolási és hálózati, az egyes előfizetésekhez óránként hozhat létre használati adatokat. A használati adatokat az erőforrás, például az erőforrás nevét, a használt előfizetés és a felhasznált mennyiség használt információkat tartalmaz. Mérőszámok azonosító erőforrásokkal kapcsolatos további tudnivalókért tekintse meg a [használati API – gyakori kérdések](azure-stack-usage-related-faq.md) cikk.

Miután a használati adatokat összegyűjtötte-e, [Azure jelentett](azure-stack-usage-reporting.md) létrehozni egy számlázási, amely az Azure számlázási portálján keresztül lehet megtekinteni. 


> [!NOTE]
> Használati adatok jelentése nincs szükség Azure verem szoftverfejlesztői készlet és integrált Azure verem rendszer felhasználók számára, akik a kapacitás modellben licenc. Azure-készletben licenceléssel kapcsolatos további tudnivalókért tekintse meg a [csomagolás és árképzési](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf) Adatlap.

A számlázási Azure-portálon a kiszabható erőforrásokra vonatkozó használati adatokat jeleníti meg. Azure verem terhelhető erőforrásokat, rögzíti a használati adatok összefoglalásához szélesebb körben állítja be a erőforrásokat, amelyek a verem Azure környezetben a REST API-k vagy a Powershellen keresztül érheti el. Az Azure verem operátorok előfizetéseket felhasználó kérheti le a használati adatok. Egyéni felhasználók számára csak beszerezheti használati adatait. 

## <a name="usage-reporting-for-multitenant-cloud-service-providers"></a>Jelentéskészítés a több-bérlős Felhőszolgáltatók kihasználtsága

Egy több-bérlős Felhőszolgáltató (CSP) rendelkező Azure verem használatával számos ügyfél érdemes külön-külön jelentést minden egyes ügyfél-használati, hogy a szolgáltató díjat használati különböző Azure-előfizetésekhez. 

Minden ügyfél az identitásukat egy másik Azure Active Directory (Azure AD)-bérlője által képviselt lesz. Az Azure verem minden Azure AD-bérlő hozzárendelése egy CSP előfizetés támogatja. Az alapszintű Azure verem regisztráció bérlők és az előfizetések adhat hozzá. Az alap regisztráció történik az összes Azure-halom. Ha az előfizetés nincs regisztrálva a bérlő számára, a felhasználó továbbra is az Azure-vermet, és használatát a program elküldi az alap regisztrációjához használt előfizetés. 


## <a name="next-steps"></a>További lépések

[Az Azure verem regisztrálása](azure-stack-registration.md)

[Jelentés Azure verem használati adatait az Azure-bA](azure-stack-usage-reporting.md)

[Szolgáltató Erőforrás kihasználtsága API](azure-stack-provider-resource-api.md)

[A bérlői API-erőforrás-használat](azure-stack-tenant-resource-usage-api.md)

[Használati kapcsolatos gyakori kérdések](azure-stack-usage-related-faq.md)