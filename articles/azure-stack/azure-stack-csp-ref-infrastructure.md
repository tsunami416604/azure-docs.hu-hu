---
title: "Jelentéskészítési infrastruktúra felhőszolgáltatóknál Azure verem használati |} Microsoft Docs"
description: "Azure verem tartalmaz, akkor fordul elő, és az Azure-bA továbbítja azt a használat követése szükséges infrastruktúrát."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2018
ms.author: mabrigg
ms.reviewer: alfredo
ms.openlocfilehash: 4ac808e0e85b1daeb54a3f2fd7bec0a7c10aa13e
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2018
---
## <a name="usage-reporting-infrastructure-for-cloud-service-providers"></a>Jelentéskészítési infrastruktúra felhőszolgáltatóknál kihasználtsága

Azure verem tartalmaz, akkor fordul elő, és az Azure-bA továbbítja azt a használat követése szükséges infrastruktúrát. Az Azure-ban Azure kereskedelmi dolgozza fel a használati adatok, és a megfelelő Azure-előfizetésekhez, használati díja a használati módok akkor történik meg a globális Azure felhőben azonos módon.

Vegye figyelembe, hogy bizonyos koncepciók megegyeznek-e globális Azure és az Azure-verem között kell lennie. Az Azure verem helyi előfizetések, amelyek megfelelnek a hasonló szerepet Azure-előfizetéshez tartozik. Csak érvényes helyileg a helyi előfizetések. Helyi előfizetések Azure-előfizetések vannak leképezve, amikor használata az Azure-bA továbbítja.

Az Azure verem helyi használati mérőszámok rendelkezik. Helyi használata az Azure kereskedelmi használt mérőszámok van leképezve. A mérési azonosítók azonban eltérőek. Nincsenek további mérőszámok helyileg mint egy a Microsoft számlázási használ.

Néhány hogyan szolgáltatások ára Azure verem és az Azure közötti különbségek vannak. Például Azure-készletben, a költség, a virtuális gépek csak alapul vcore/óra, az összes Virtuálisgép-sorozat Azure eltérően a azonos sebességet. A hiba oka, hogy a globális Azure-ban a különböző árak különböző hardver tükrözik. Azure-készletben az ügyfél a hardver nyújt, így nem ok arra, hogy a virtuális gép különböző osztályú különböző díjakat díjat számítanak.

Azt is megtudhatja, az Azure-verem mérőszámok használt kereskedelmi és az árak Partnerközpontját, ugyanúgy az Azure-szolgáltatásokkal kapcsolatos:

1. Partnerközpontját, keresse meg a **irányítópult menü** > **árak és ajánlatok**.
2. A **használatalapú szolgáltatások**, jelölje be **aktuális**.
3. Nyissa meg a **a globális CSP árlista Azure** táblázat.
4. A szűrés **régió Azure verem =**.

## <a name="usage-and-billing-error-codes"></a>Használati és számlázási hibakódok

A következő hibaüzenetek is lehet észlelt, amikor egy regisztrációban bérlők hozzáadása.

| Hiba                           | Részletek                                                                                                                                                                                                                                                                                                                           | Megjegyzések                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
|---------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| RegistrationNotFound            | A megadott bejegyzés nem található. Győződjön meg arról, hogy helyesen adta meg a következő információkat:<br>1. Előfizetés-azonosító (megadott érték: _előfizetés-azonosító_),<br>2. Erőforráscsoport (megadott érték: _erőforráscsoport_),<br>3. Regisztrációs neve (megadott érték: _regisztrációs neve_).                             | Ez a hiba általában akkor fordul elő, ha az adatokat a kezdeti regisztráció mutató nem helyes. Ellenőrizze az erőforráscsoportot és a regisztráció nevét kell, ha megtalálja az Azure portálon, ehhez listázza az összes erőforrást. Ha egynél több regisztrációs erőforrás, nézze meg a CloudDeploymentID tulajdonságai, és válassza ki a regisztrációt, akiknek CloudDeploymentID megegyezik a felhőben. A CloudDeploymentID található, használhatja a PowerShell a Azure veremben:<br>`$azureStackStampInfo = Invoke-Command -Session $session -ScriptBlock { Get-AzureStackStampInformation }` |
| BadCustomerSubscriptionId       | A megadott _előfizetési azonosító_ és a _regisztrációs neve_ előfizetés-azonosító nem tulajdonosa a ugyanazon Microsoft Felhőbeli szolgáltatás szolgáltatója. Ellenőrzi, hogy a felhasználói előfizetés-azonosító helyes-e. Ha a probléma továbbra is fennáll, forduljon a támogatási szolgálathoz. | Ez a hiba akkor fordul elő, az ügyfél-előfizetést egy CSP-előfizetést, de azt összesíti egy CSP-partnernek eltér, amelyhez a kezdeti regisztrációhoz használt előfizetés összesíti. Ez az ellenőrzés elkerülése érdekében a ki nem felelős az Azure-verem használt kriptográfiai Szolgáltató partner számlázási következtében történik.                                                                                                                                                                                                                                                                          |
| InvalidCustomerSubscriptionId   | A "_előfizetési azonosító_" érvénytelen. Győződjön meg arról, hogy egy érvényes Azure-előfizetéshez biztosított.                                                                                                                                                                         |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| CustomerSubscriptionNotFound    | _Előfizetési azonosító_ _registration néven nem található. Ellenőrizze, hogy egy érvényes Azure-előfizetés használatban van, és, hogy az előfizetés-azonosító a regisztráció, a PUT művelettel lett hozzáadva.                                                   | Ez a hiba akkor fordul elő, a bérlő előfizetés hozzá lett adva, és az ügyfél előfizetése nem található a regisztrációs társítandó felelőség súlyossága közben. Az ügyfél nincs hozzáadva a regisztrációhoz, vagy helytelenül lett írva az előfizetés-azonosító.                                                                                                                                                                                                                                                                                                                                |
| UnauthorizedCspRegistration     | A megadott _regisztrációs neve_ több-bérlős használata nem engedélyezett. E-mail küldése azstCSP@microsoft.com és regisztrációs nevét, erőforráscsoport és az előfizetés-azonosító a regisztrációhoz használt.                                                                                    | Hagyja jóvá a több-bérlős Microsoft megkezdése bérlők hozzáadása előtt kell egy regisztrációs. Tekintse át a szakasz regisztrálása bérlők kapcsolódó további tájékoztatást a dokumentumban.                                                                                                                                                                                                                                                                                                                                                                                                             |
| CustomerSubscriptionsNotAllowed | Ügyfél-előfizetések műveletek nem támogatottak a leválasztott ügyfelek. Ez a funkció használatához regisztrálja újra licenceléshez kell fizetnie használhatja.                                                                                                                                                                    | A bérlők hozzáadni kívánt, amelyhez a regisztráció Ez azt jelenti, hogy az kapacitás, regisztráció, a regisztráció létrehozásakor, a paraméter BillingModel kapacitás lett megadva. Regisztráció használata csak fizetést hozzáadása a bérlők engedélyezettek. Regisztrálja újra a BillingModel PayAsYouUse paraméter használatával kell.                                                                                                                                                                                                                                                                                          |
| InvalidCSPSubscription          | A megadott _előfizetési azonosító_ nincs CSP érvényes előfizetéssel. Győződjön meg arról, hogy egy érvényes Azure-előfizetéshez biztosított.                                                                                                                                                        | Főleg valószínű, hogy az ügyfél előfizetése, hogy rosszul írta okozhatják.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| MetadataResolverBadGatewayError | Nem várt hibát adott vissza a felsőbb rétegbeli kiszolgálók egyikét. Próbálkozzon újra később. Ha a probléma továbbra is fennáll, forduljon a támogatási szolgálathoz.                                                                                                                                                                                                |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |

## <a name="terms-used-for-billing-and-usage"></a>A számlázási és a használati használt kifejezések

Az alábbi kifejezések és fogalmak használt használati és számlázási Azure verem:

| Időtartam | Meghatározás |
| --- | --- |
| Közvetlen CSP-partner | A közvetlen Cloud Solution Provider (CSP) partner számla fogadása közvetlenül a Microsoft Azure és az Azure-verem használati és váltók ügyfelek közvetlenül. |
| Közvetett kriptográfiai Szolgáltató | Közvetett viszonteladók közvetett szolgáltatóval (más néven a terjesztő) működik. A viszonteladók számára szükséges végfelhasználóhoz; a közvetett szolgáltató Microsoft számlázási kapcsolattal rendelkezik, számlázási ügyfél kezeli, és nyújt további szolgáltatásokat, mint a terméktámogatási szolgálathoz. |
| Záró ügyfél | Végfelhasználók, a vállalatok számára, és az alkalmazások és az Azure-veremben futó egyéb alkalmazások és szolgáltatások saját állami intézményekhez. |

## <a name="next-steps"></a>További lépések

 - A kriptográfiai Szolgáltató program kapcsolatos további információkért lásd: [Cloud Solution Provider programot](https://partnercenter.microsoft.com/en-us/partner/programs).
 - Hogyan lehet lekérni az erőforrás-használati adatait az Azure oszlopból kapcsolatos további információkért lásd: [használati és számlázási Azure verem](/azure-stack-billing-and-chargeback.md).
