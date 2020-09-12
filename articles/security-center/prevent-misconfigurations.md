---
title: A hibás konfigurációk megelőzése Azure Security Center
description: Megtudhatja, hogyan használhatja Security Center "betartatása" és "megtagadás" beállításait a javaslatok részleteinek oldalain.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 09/08/2020
ms.author: memildin
ms.openlocfilehash: ceafbe1fd1682fc5e92ab3048ed09866fb9ebbdf
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/09/2020
ms.locfileid: "89570455"
---
# <a name="prevent-misconfigurations-with-enforcedeny-recommendations"></a>A helytelen konfiguráció megelőzése a kényszerítő/megtagadási javaslatokkal

A biztonsági incidensek jelentős oka a biztonság. A Security Center mostantól lehetővé teszi az új erőforrások helytelen konfigurációjának *megelőzését* az adott javaslatok tekintetében. 

Ez a funkció segít megőrizni a számítási feladatokat, és stabilizálni tudja a biztonságos pontszámot.

A biztonságos konfiguráció egy adott javaslat alapján történő érvényesítése két üzemmódban érhető el:

- A Azure Policy **megtagadásának** hatására leállíthatja a nem megfelelő állapotú erőforrásokat

- A **kikényszerítés** lehetőség használatával kihasználhatja az Azure-szabályzat **DeployIfNotExist** hatását, és a létrehozáskor automatikusan elháríthatja a nem megfelelő erőforrásokat.
 
Ez a kiválasztott biztonsági javaslatok esetében elérhető, és az erőforrás részletei lap tetején található.

## <a name="prevent-resource-creation"></a>Erőforrás-létrehozás megakadályozása

1. Nyissa meg azt a javaslatot, amely szerint az új erőforrásoknak meg kell felelniük, és válassza a **Megtagadás** gombot az oldal tetején.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-deny-button.png" alt-text="Javaslat lap Kiemelt megtagadás gombja":::

    Megnyílik a konfigurációs ablaktábla, amely felsorolja a hatókör beállításait. 

1. A hatókör beállításához válassza ki a megfelelő előfizetést vagy felügyeleti csoportot.

    > [!TIP]
    > A sor végén található három pont használatával egyetlen előfizetés módosítható, vagy a jelölőnégyzetek használatával több előfizetést vagy csoportot is kijelölhet, majd kiválaszthatja, **hogy megtagadja a módosítást**.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-prevent-resource-creation.png" alt-text="Azure Policy megtagadás hatókörének beállítása":::


## <a name="enforce-a-secure-configuration"></a>Biztonságos konfiguráció érvényesítése

1. Nyissa meg azt a javaslatot, amelybe telepíteni kívánja a sablon központi telepítését, ha az új erőforrások nem elégítik ki azt, majd kattintson a **kikényszerítés** gombra az oldal tetején.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-enforce-button.png" alt-text="Javaslat lap Kiemelt kikényszerített gombja":::

    Megnyílik a konfigurációs ablaktábla az összes házirend-konfigurációs lehetőséggel. 

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-enforce-config.png" alt-text="Konfigurációs beállítások érvényesítése":::

1. Adja meg a hatókört, a hozzárendelés nevét és az egyéb kapcsolódó beállításokat.

1. Válassza a **Felülvizsgálat + létrehozás** lehetőséget.

## <a name="recommendations-with-denyenforce-options"></a>Megtagadási/kikényszerítő lehetőségekkel kapcsolatos javaslatok

A következő javaslatok használhatók a **megtagadási** beállítással:

- A virtuális gépeket át kell telepíteni az új Azure Resource Manager erőforrásokra
- A Storage-fiókokat át kell telepíteni az új Azure Resource Manager erőforrásokra
- A RootManageSharedAccessKey kivételével az összes engedélyezési szabályt el kell távolítani az Event hub-névtérből
- A RootManageSharedAccessKey kivételével minden engedélyezési szabályt el kell távolítani Service Bus névtérből
- Engedélyezni kell a tárfiókokba történő biztonságos átvitelt
- Csak a Redis Cache biztonságos kapcsolatai legyenek engedélyezve
- Az Automation-fiók változóit titkosítani kell
- Service Fabric-fürtök esetében csak Azure Active Directoryt kell használnia az ügyfél-hitelesítéshez
- Service Fabric-fürtökön a ClusterProtectionLevel tulajdonságot EncryptAndSign értékre kell beállítani
- Nem korlátozott hálózati hozzáférés naplózása a Storage-fiókokhoz


A következő javaslatok használhatók a **kényszerítő** beállítással:

- A Logic Apps lévő diagnosztikai naplókat engedélyezni kell
- A Data Lake Analytics lévő diagnosztikai naplókat engedélyezni kell
- A IoT Hub lévő diagnosztikai naplókat engedélyezni kell
- A Batch-fiókokban lévő diagnosztikai naplókat engedélyezni kell
- A Azure Stream Analytics lévő diagnosztikai naplókat engedélyezni kell
- A Service Bus lévő diagnosztikai naplókat engedélyezni kell
- A keresési szolgáltatásokban engedélyezni kell a diagnosztikai naplókat.
- Az Event hub diagnosztikai naplóit engedélyezni kell
- A Virtual Machine Scale Sets lévő diagnosztikai naplókat engedélyezni kell
- A Key Vault lévő diagnosztikai naplókat engedélyezni kell
- Az SQL Server naplózását engedélyezni kell
- A speciális adatbiztonságot engedélyezni kell az SQL-kiszolgálókon



