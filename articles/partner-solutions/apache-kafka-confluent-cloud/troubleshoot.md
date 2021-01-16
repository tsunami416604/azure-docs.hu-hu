---
title: Az Azure-partneri megoldások Apache Kafka hibaelhárítása
description: Ez a cikk tájékoztatást nyújt az Azure-beli felhőalapú felhővel kapcsolatos hibaelhárításról és gyakran ismételt kérdésekről (GYIK).
ms.service: partner-services
ms.topic: conceptual
ms.date: 01/15/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: cbf166086a489165e8100dafd7c212ab6c298b41
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253525"
---
# <a name="troubleshooting-apache-kafka-for-confluent-cloud-solutions"></a>A felhőalapú megoldások Apache Kafka hibaelhárítása

Ez a dokumentum a Apache Kafka for Fluent Cloud-t használó megoldások hibaelhárításával kapcsolatos információkat tartalmaz.

Ha nem talál választ, vagy nem tudja megoldani a problémát, [hozzon létre egy kérelmet a Azure Portal](manage.md#get-support) , vagy forduljon a [Fluent támogatáshoz](https://support.confluent.io).

## <a name="cant-find-offer-in-the-marketplace"></a>Nem található ajánlat a piactéren

Az ajánlat az Azure Marketplace-en való megtalálásához kövesse az alábbi lépéseket:

1. A [Azure Portal](https://portal.azure.com)válassza az **erőforrás létrehozása** lehetőséget.
1. Apache Kafka keresése az _Fluent felhőben_.
1. Válassza ki az alkalmazás csempét.

Ha az ajánlat nem jelenik meg, forduljon a [Fluent támogatási szolgálathoz](https://support.confluent.io). A Azure Active Directory bérlői AZONOSÍTÓjának az engedélyezett bérlők listáján kell lennie. A bérlői azonosító megkeresésének megismeréséhez tekintse meg [az Azure Active Directory bérlői azonosítójának megkeresése](../../active-directory/fundamentals/active-directory-how-to-find-tenant.md)című témakört.

## <a name="conflict-error"></a>Ütközési hiba

Ha korábban már regisztrált az Fluent-felhőben, új e-mail-címet kell használnia egy másik, felhőalapú szervezeti erőforrás létrehozásához. Ha korábban regisztrált e-mail-címet használ, **ütközési** hibaüzenetet kap. Regisztráljon újra, de ezúttal egy új e-mail-címmel.

## <a name="deploymentfailed-error"></a>DeploymentFailed hiba

Ha **DeploymentFailed** hibaüzenetet kap, ellenőrizze az Azure-előfizetés állapotát. Győződjön meg róla, hogy nincs felfüggesztve, és nem rendelkezik számlázási problémákkal.

## <a name="resource-isnt-displayed"></a>Az erőforrás nem jelenik meg

Ha az **Áttekintés** vagy a **törlési** panel nem jelenik meg a portálon, próbálja meg frissíteni a lapot. Ez a hiba lehet egy átmeneti probléma a portálon. Ha ez nem működik, forduljon a [Fluent támogatási szolgálathoz](https://support.confluent.io).

Ha a felhőalapú erőforrás nem található az Azure **minden erőforrás** listájában, lépjen kapcsolatba a [Fluent támogatással](https://support.confluent.io).

## <a name="resource-creation-takes-long-time"></a>Az erőforrás létrehozása hosszú időt vesz igénybe

Ha a telepítési folyamat több mint három órát vesz igénybe, forduljon az ügyfélszolgálathoz.

Ha az üzembe helyezés meghiúsul, és a Felhőbeli erőforrás állapota állapotú `Failed` , törölje az erőforrást. A törlés után próbálkozzon újra az erőforrás létrehozásával.

## <a name="offer-plan-doesnt-load"></a>Az ajánlati csomag nem töltődik be

Ez a hiba lehet egy átmeneti probléma a Azure Portal. Próbálja újból telepíteni az ajánlatot.

## <a name="unable-to-delete"></a>A(z)

Ha nem tudja törölni a Fluent-erőforrásokat, ellenőrizze, hogy rendelkezik-e az erőforrás törléséhez szükséges engedélyekkel. Lehetővé kell tenni a Microsoft. Fluent/*/delete műveletek végrehajtását. További információ az engedélyek megtekintéséről: [Az Azure szerepkör-hozzárendelések listázása a Azure Portal használatával](../../role-based-access-control/role-assignments-list-portal.md).

Ha rendelkezik a megfelelő engedélyekkel, de továbbra sem tudja törölni az erőforrást, forduljon az [Fluent támogatási szolgálathoz](https://support.confluent.io). Ez az állapot az adatmegőrzési szabályzattal kapcsolatban lehet. A Fluent támogatással a szervezet és az e-mail-cím is törölhető.

## <a name="unable-to-use-single-sign-on"></a>Nem lehet használni az egyszeri bejelentkezést

Ha az SSO nem működik az Fluent Cloud SaaS-portálon, ellenőrizze, hogy a helyes Azure Active Directory e-mailt használja-e. Emellett hozzájárult ahhoz is, hogy hozzáférést biztosítson a felhőalapú szoftver (SaaS) portálhoz. További információ: az [egyszeri bejelentkezésre vonatkozó útmutató](manage.md#single-sign-on).

Ha a probléma továbbra is fennáll, forduljon a [Fluent támogatási szolgálathoz](https://support.confluent.io).

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [kezelheti Apache Kafka-példányát](manage.md) az Fluent felhőhöz.
