---
title: A Key Vault-értesítések fogadása és reagálás Azure Event Grid
description: Ismerje meg, hogyan integrálhatja a Key Vaultt a Azure Event Gridsal.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: tutorial
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: 3b24da4d988554da240baba2984df44ff4744aaf
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73464104"
---
# <a name="how-to-receive-and-respond-to-key-vault-notifications-with-azure-event-grid-preview"></a>Útmutató: a Key Vault-értesítések fogadása és reagálás Azure Event Grid (előzetes verzió)

A jelenleg előzetes verzióban elérhető Azure Event Gridtel Key Vault integráció lehetővé teszi a felhasználók számára, hogy a Key vaultban tárolt titkos kód állapotának változásakor értesítést kapjanak. A szolgáltatás áttekintését lásd: [Key Vault figyelése Azure Event Gridsal](event-grid-overview.md).

Ebből az útmutatóból megtudhatja, hogyan fogadhat Key Vault-értesítéseket a Azure Event Gridon keresztül, és hogyan reagálhat az állapot változásaira a Azure Automation használatával.

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.
- Egy kulcstartó az Azure-előfizetésében. Az Azure CLI használatával gyorsan létrehozhat egy új kulcstartót az [Azure Key Vault titkos kód beállítása és lekérése](quick-create-cli.md) a következő lépésekkel:

## <a name="concepts"></a>Alapelvek

Az Azure Event Grid egy felhőalapú eseménykezelési szolgáltatás. Ebben az útmutatóban előfizet a Key Vault eseményeire, és átirányítja az eseményeket Azure Automationra. Ha a Key Vault egyik titka hamarosan lejár, Event Grid értesítést kap az állapot változásáról, és egy HTTP-BEJEGYZÉST küld a végpontnak. Egy webhook ezután elindítja a PowerShell-szkript Azure Automation végrehajtását.

![image](media/image1.png)

## <a name="create-an-azure-automation-account"></a>Azure Automation-fiók létrehozása

Hozzon létre egy Azure Automation fiókot a [Azure Portalon](https://portal.azure.com)keresztül.

1.  Nyissa meg a portal.azure.com, és jelentkezzen be az előfizetésbe.

1.  A keresőmezőbe írja be az "Automation-fiókok" kifejezést.

1.  A keresősáv legördülő menüjének "szolgáltatások" szakaszában válassza az "Automation-fiókok" lehetőséget.

1.  Kattintson az Add (Hozzáadás) parancsra.

    ![](media/image2.png)

1.  Töltse ki a szükséges információkat az "Automation-fiók hozzáadása" panelen, és válassza a "létrehozás" lehetőséget.

## <a name="create-a-runbook"></a>Runbook létrehozása

Miután a Azure Automation fiók elkészült, hozzon létre egy runbook.

![](media/image3.png)

1.  Válassza ki az imént létrehozott Automation-fiókot.

1.  A folyamat-automatizálás szakaszban válassza a "Runbookok" lehetőséget.

1.  Kattintson a "runbook létrehozása" elemre.

1.  Nevezze el a runbook, és válassza a "PowerShell" lehetőséget a runbook típusaként.

1.  Kattintson a létrehozott runbook, és válassza a "szerkesztés" gombot.

1.  Adja meg a következő kódot (tesztelési célokra), majd kattintson a "közzététel" gombra. Ez a művelet a POST kérelem fogadásának eredményét jeleníti meg.

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

![](media/image4.png)

## <a name="create-a-webhook"></a>Webhook létrehozása

Most hozzon létre egy webhookot az újonnan létrehozott runbook elindításához.

1.  Válassza a "webhookok" lehetőséget az imént közzétett runbook erőforrások szakaszából.

1.  Kattintson a "webhook hozzáadása" elemre.

    ![](media/image5.png)

1.  Válassza az "új webhook létrehozása" lehetőséget.

1. Nevezze el a webhookot, állítsa be a lejárati dátumot, és **másolja az URL-címet**.

    > [!IMPORTANT] 
    > A létrehozás után nem tekintheti meg az URL-címet. Győződjön meg arról, hogy mentett egy biztonságos helyet, ahol az útmutató hátralévő részében elérheti azt.

1. Kattintson a paraméterek és futtatási beállítások elemre, majd válassza az OK gombot. Ne adjon meg paramétereket. Ez lehetővé teszi a "létrehozás" gombot.

1. Válassza az OK gombot, és válassza a létrehozás lehetőséget.

    ![](media/image6.png)

## <a name="create-an-event-grid-subscription"></a>Event Grid-előfizetés létrehozása

Hozzon létre Event Grid-előfizetést a [Azure Portal](https://portal.azure.com)használatával.

1.  Nyissa meg a Azure Portal a következő hivatkozással: https://portal.azure.com/?Microsoft_Azure_KeyVault_ShowEvents=true&Microsoft_Azure_EventGrid_publisherPreview=true

1.  Nyissa meg a Key vaultot, és válassza az "események" fület. Ha nem látja az események lapot, győződjön meg arról, hogy a [portál előzetes verzióját](https://ms.portal.azure.com/?Microsoft_Azure_KeyVault_ShowEvents=true&Microsoft_Azure_EventGrid_publisherPreview=true)használja.

    ![](media/image7.png)

1.  Kattintson a "+ esemény-előfizetés" gombra.

1.  Hozzon létre egy leíró nevet az előfizetéshez.

1.  Válassza a "Event Grid séma" lehetőséget.

1.  A "témakör-erőforrás" értéknek kell lennie az állapot változásaihoz figyelni kívánt kulcstartónak.

1.  A "szűrés az események típusaihoz" beállításnál hagyja az összes bejelölt elemet ("9 kijelölt").

1.  A "végpont típusa" beállításnál válassza a "webhook" lehetőséget.

1.  Válassza a "végpont kiválasztása" lehetőséget. Az új környezet ablaktáblában illessze be a webhook URL-címét a [webhook létrehozása](#create-a-webhook) lépésből az "előfizetői végpont" mezőbe.

1.  Válassza a kijelölés megerősítése lehetőséget a helyi ablaktáblán.

1.  Válassza a létrehozás lehetőséget.

    ![](media/image8.png)

## <a name="test-and-verify"></a>Tesztelés és ellenőrzés

Ellenőrizze, hogy a Event Grid-előfizetése tulajdonság konfigurálva van-e.  Ez a teszt azt feltételezi, hogy előfizetett a "titkos új verzió létrehozása" értesítésre a [Event Grid előfizetés létrehozása](#create-an-event-grid-subscription)című cikkben, és hogy rendelkezik a szükséges jogosultságokkal a titkos kulcs új verziójának létrehozásához a kulcstartóban.

![](media/image9.png)

![](media/image10.png)

1.  Nyissa meg a Key vaultot a Azure Portal

1.  Hozzon létre egy új titkot. Tesztelési célból állítsa a lejárat dátumát a következő napra.

1.  Navigáljon a Key Vault Events (események) lapjára.

1.  Válassza ki a létrehozott Event Grid-előfizetést.

1.  A metrikák területen ellenőrizze, hogy van-e esemény rögzítve. A rendszer két eseményt vár: SecretNewVersion és SecretNearExpiry. Ez ellenőrzi, hogy az Event Grid sikeresen rögzítette-e a titkos kulcs állapotának változását a kulcstartóban.

    ![](media/image11.png)

1.  Nyissa meg Azure Automation-fiókját.

1.  Válassza a "Runbookok" fület, és válassza ki a létrehozott runbook.

1.  Válassza a "webhookok" fület, és győződjön meg róla, hogy az "utolsó indítású" időbélyeg az új titkos kulcs létrehozásakor 60 másodpercen belül van.  Ezzel megerősíti, hogy a Event Grid közzétett egy BEJEGYZÉST a webhookba a kulcstartóban lévő állapotváltozás esemény részleteivel, és a webhook aktiválva lett.

    ![](media/image12.png)

1. Térjen vissza a Runbook, és válassza az "áttekintés" lapot.

1. Tekintse meg a legutóbbi feladatok listáját. Látnia kell, hogy a rendszer létrehozta a feladatot, és befejezte az állapotot.  Ezzel megerősíti, hogy a webhook elindította a runbook a parancsfájl végrehajtásának megkezdéséhez.

    ![](media/image13.png)

1. Válassza ki a legutóbbi feladatot, és tekintse meg az Event gridből a webhookba küldendő POST kérelmet. Vizsgálja meg a JSON-t, és ellenőrizze, hogy helyesek-e a Key Vault és az eseménytípus paraméterei. Ha a JSON-objektum "eseménytípus" paramétere megegyezik a Key vaultban bekövetkezett eseménnyel (ebben a példában a Microsoft. kulcstartó. SecretNearExpiry), a teszt sikeres volt.

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="unable-to-create-event-subscription"></a>Nem sikerült létrehozni az esemény-előfizetést

Regisztrálja újra Event Grid és Key Vault szolgáltatót az Azure-előfizetési erőforrás-szolgáltatókban. Lásd: [Azure Resource Providers és types](../azure-resource-manager/resource-manager-supported-services.md).

## <a name="next-steps"></a>További lépések

Gratulálunk! Ha követte a fenti lépéseket, most már készen áll arra, hogy programozott módon válaszoljon a Key vaultban tárolt titkok állapotának változásaira.

Ha egy lekérdezésen alapuló rendszert használ, hogy megkeresse a kulcsok állapotának változásait a kulcstartókban, térjen át az értesítési szolgáltatás használatára. A runbook is lecserélheti a kód segítségével, hogy programozott módon megújítsa a titkokat, amikor hamarosan lejárnak.

Részletek:

- [Azure Key Vault áttekintése](key-vault-overview.md)
- [Azure Event Grid áttekintése](../event-grid/overview.md)
- [Key Vault figyelése Azure Event Grid (előzetes verzió)](event-grid-overview.md)
- [Azure Key Vault Azure Event Gridi esemény sémája (előzetes verzió)](../event-grid/event-schema-key-vault.md)
- [Az Azure Automation áttekintése](../automation/index.yml)
