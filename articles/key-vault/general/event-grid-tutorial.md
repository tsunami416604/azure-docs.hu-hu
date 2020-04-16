---
title: Kulcstartó-értesítések fogadása és megválaszolása az Azure Event Griddel
description: Ismerje meg, hogyan integrálhatja a Key Vaultot az Azure Event Griddel.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: 300abc8ca6798866295c865e68bf5ec24a00cf5f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423255"
---
# <a name="receive-and-respond-to-key-vault-notifications-with-azure-event-grid-preview"></a>A kulcstartó-értesítések fogadása és megválaszolása az Azure Event Grid del (előzetes verzió)

Az Azure Key Vault integráció az Azure Event Grid (jelenleg előzetes verzióban) lehetővé teszi a felhasználói értesítést, ha a titkos tárolt titkos kulcstartóban tárolt állapot megváltozott. A funkció áttekintését a [Key Vault figyelése az Eseményrácsgal](event-grid-overview.md)című témakörben találja.

Ez az útmutató bemutatja, hogyan fogadhatja a Key Vault-értesítéseket az Event Griden keresztül, és hogyan reagálhat az állapotváltozásokra az Azure Automation en keresztül.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.
- Az Azure-előfizetés egyik kulcstartója. Gyorsan létrehozhat egy új kulcstartót a Készlet és [az Azure Key Vault titkos adatainak az Azure CLI használatával történő lekérésével.](../secrets/quick-create-cli.md)

## <a name="concepts"></a>Alapelvek

Az Event Grid a felhő eseményszolgáltatási szolgáltatása. Az útmutató ban leírt lépéseket követve előfizethet a Key Vault eseményeire, és az eseményeket az Automation-be irányíthatja. Amikor a kulcstartó egyik titkos kulcsa hamarosan lejár, event grid értesítést kap az állapotváltozás, és egy HTTP POST a végpontra. A webhook majd elindítja a PowerShell-parancsfájl Automation-végrehajtása.

![HTTP POST folyamatábra](../media/image1.png)

## <a name="create-an-automation-account"></a>Automation-fiók létrehozása

Hozzon létre egy Automation-fiókot az [Azure Portalon](https://portal.azure.com)keresztül:

1.  Lépjen a portal.azure.com, és jelentkezzen be az előfizetésbe.

1.  A keresőmezőbe írja be az **Automation-fiókok kifejezést.**

1.  A keresősáv legördülő listájának **Szolgáltatások** szakaszában válassza az **Automatizálási fiókok lehetőséget.**

1.  Válassza a **Hozzáadás** lehetőséget.

    ![Automatizálási fiókok ablaktábla](../media/image2.png)

1.  Adja meg a szükséges adatokat az **Automatizálási fiók hozzáadása** ablaktáblán, majd válassza a **Létrehozás lehetőséget.**

## <a name="create-a-runbook"></a>Runbook létrehozása

Miután az Automation-fiók készen áll, hozzon létre egy runbookot.

![Runbook felhasználói felület létrehozása](../media/image3.png)

1.  Válassza ki az imént létrehozott Automation-fiókot.

1.  Válassza a **Runbookok** lehetőséget a **Folyamatautomatizálás csoportban.**

1.  Válassza **a Runbook létrehozása**lehetőséget.

1.  Nevezze el a runbookot, és válassza a **PowerShell** a runbook típusát.

1.  Jelölje ki a létrehozott runbookot, majd kattintson a **Szerkesztés** gombra.

1.  Írja be a következő kódot (tesztelési célokra), és válassza a **Közzététel** gombot. Ez a művelet a beérkezett POST kérés eredményét adja vissza.

```azurepowershell
param
(
[Parameter (Mandatory = $false)]
[object] $WebhookData
)

#If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData) {

#rotate secret:
#generate new secret version in key vault
#update db/service with generated secret

#Write-Output "WebhookData <$WebhookData>"
Write-Output $WebhookData.RequestBody
}
else
{
# Error
write-Error "No input data found." 
}
```

![Runbook felhasználói felülete](../media/image4.png)

## <a name="create-a-webhook"></a>Webhook létrehozása

Hozzon létre egy webhook az újonnan létrehozott runbook aktiválásához.

1.  Válassza ki a **webhookok** az **erőforrások** szakaszban a runbook az imént közzétett.

1.  Válassza **a Webhook hozzáadása**lehetőséget.

    ![Webhook hozzáadása gomb](../media/image5.png)

1.  Válassza **az Új webhook létrehozása**lehetőséget.

1. Nevezze el a webhookot, állítson be egy lejárati dátumot, és másolja az URL-címet.

    > [!IMPORTANT] 
    > Az URL-cím létrehozása után nem tekinthető meg. Győződjön meg arról, hogy egy másolatot biztonságos helyre ment, ahol az útmutató hátralévő részében elérheti azt.

1. Válassza a **Paraméterek lehetőséget, majd futtassa a beállításokat,** majd kattintson **az OK gombra.** Ne adjon meg paramétereket. Ez lehetővé teszi a **Létrehozás** gombot.

1. Válassza **az OK,** majd a **Létrehozás**lehetőséget.

    ![Új Webhook felhasználói felület létrehozása](../media/image6.png)

## <a name="create-an-event-grid-subscription"></a>Event Grid-előfizetés létrehozása

Hozzon létre egy Event Grid-előfizetést az [Azure Portalon](https://portal.azure.com)keresztül.

1.  Nyissa meg a kulcstartót, és válassza az **Események** lapot. Ha nem látja, ellenőrizze, hogy [a portál előzetes verzióját használja-e.](https://ms.portal.azure.com/?Microsoft_Azure_KeyVault_ShowEvents=true&Microsoft_Azure_EventGrid_publisherPreview=true)

    ![Események lap az Azure Portalon](../media/image7.png)

1.  Válassza az **Esemény-előfizetés** gombot.

1.  Hozzon létre egy leíró nevet az előfizetéshez.

1.  Válassza **az Eseményrács sémáját**.

1.  **A Témakör-erőforrás** nak kell lennie a figyelni kívánt kulcstartónak az állapotváltozásokhoz.

1.  A **Szűrés eseménytípusokra**mezőben hagyja az összes beállítást kijelölve (**9 kijelölve**).

1.  A **Végpont típusa** mezőben válassza a **Webhook** lehetőséget.

1.  Válassza **a Végpont kiválasztása**lehetőséget. Az új környezet ablaktáblán illessze be a webhook URL-címét a [Webhook létrehozása](#create-a-webhook) lépésből az **Előfizetői végpont** mezőbe.

1.  A környezeti ablaktáblán válassza a **Kijelölés megerősítése** lehetőséget.

1.  Kattintson a **Létrehozás** gombra.

    ![Esemény-előfizetés létrehozása](../media/image8.png)

## <a name="test-and-verify"></a>Tesztelés és ellenőrzés

Ellenőrizze, hogy az Event Grid-előfizetés megfelelően van-e konfigurálva. Ez a teszt feltételezi, hogy előfizetett a "Titkos új verzió létrehozva" értesítésre az [Event Grid létrehozása előfizetésben,](#create-an-event-grid-subscription)és rendelkezik a szükséges engedélyekkel a titkos kulcs új verziójának létrehozásához egy key vaultban.

![Az Event Grid-előfizetés tesztelési konfigurációja](../media/image9.png)

![Titkos létrehozása ablaktábla](../media/image10.png)

1.  Nyissa meg a kulcstartót az Azure Portalon.

1.  Hozzon létre egy új titkot. Tesztelési célokra állítsa a lejárati dátumot a következő napra.

1.  A kulcstartó **Események** lapján válassza ki a létrehozott Event Grid-előfizetést.

1.  A **Metrikák csoportban**ellenőrizze, hogy egy esemény rögzítésre került-e. Két esemény várható: SecretNewVersion és SecretNearExpiry. Ezek az események ellenőrzik, hogy az Event Grid sikeresen rögzítette a titkos kulcs állapotváltozását a key vaultban.

    ![Mérőszámok ablaktábla: ellenőrizze a rögzített eseményeket](../media/image11.png)

1.  Nyissa meg az Automation-fiókot.

1.  Válassza a **Runbookok** lapot, majd válassza ki a létrehozott runbookot.

1.  Jelölje ki a **Webhooks** lapot, és ellenőrizze, hogy az "utolsó aktivált" időbélyegző az új titkos titok létrehozásától számított 60 másodpercen belül van-e. Ez az eredmény megerősíti, hogy az Event Grid postaaaaa a webhook az esemény részleteit a kulcstartóban az állapotváltozás, és hogy a webhook aktiválódott.

    ![Webhookok lap, Utolsó aktivált időbélyegző](../media/image12.png)

1. Térjen vissza a runbookhoz, és válassza az **Áttekintés** lapot.

1. Tekintse meg a **Legutóbbi feladatok** listát. Látnia kell, hogy egy feladat jött létre, és hogy az állapot befejeződött. Ez megerősíti, hogy a webhook aktiválta a runbookot a parancsfájl végrehajtásának megkezdéséhez.

    ![Webhook legutóbbi feladatok listája](../media/image13.png)

1. Válassza ki a legutóbbi feladatot, és tekintse meg a POST kérés, amely az Event Grid a webhook. Vizsgálja meg a JSON, és győződjön meg arról, hogy a paraméterek a key vault és az esemény típusa helyesek. Ha a JSON-objektum "eseménytípus" paramétere megegyezik a key vaultban történt eseménnyel (ebben a példában a Microsoft.KeyVault.SecretNearExpiry), a teszt sikeres volt.

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="you-cant-create-an-event-subscription"></a>Nem hozhat létre esemény-előfizetést

Regisztrálja újra az Event Gridet és a kulcstároló-szolgáltatót az Azure-előfizetés-erőforrás-szolgáltatókban. Lásd: [Azure-erőforrás-szolgáltatók és -típusok.](../../azure-resource-manager/management/resource-providers-and-types.md)

## <a name="next-steps"></a>További lépések

Gratulálunk! Ha helyesen követte ezeket a lépéseket, most már készen áll a kulcstartóban tárolt titkos kulcsok állapotváltozásainak programozott válaszolására.

Ha egy lekérdezési alapú rendszert használ a titkos kulcsok állapotváltozásainak kereséséhez a kulcstartókban, most már használhatja ezt az értesítési funkciót. A runbookban lévő tesztparancsfájlt is lecserélheti a kódra, amely programozott módon megújítja a titkos kulcsokat, amikor azok hamarosan lejárnak.

További információ:


- Áttekintés: [Key Vault figyelése az Azure Event Griddel (előzetes verzió)](event-grid-overview.md)
- Útmutató: [E-mailek fogadása, ha egy kulcstartó titkos titkára megváltozik](event-grid-logicapps.md)
- [Azure Event Grid eseménysémája az Azure Key Vaulthoz (előzetes verzió)](../../event-grid/event-schema-key-vault.md)
- [Az Azure Key Vault áttekintése](overview.md))
- [Azure Event Grid – áttekintés](../../event-grid/overview.md)
- [Az Azure Automation áttekintése](../../automation/index.yml)
