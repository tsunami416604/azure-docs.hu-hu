---
title: IoT-megoldásgyorsítók – gyakori kérdések – Azure | Microsoft dokumentumok
description: Ez a cikk választ ad az IoT-megoldásgyorsítókkal kapcsolatos gyakori kérdésekre. Ez magában foglalja a GitHub-adattárakra mutató hivatkozásokat.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: 7a2b167f90b4ec79f5fa515ded1c676ca7085e72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75647713"
---
# <a name="frequently-asked-questions-for-iot-solution-accelerators"></a>Gyakran ismételt kérdések az IoT-megoldásgyorsítókról

Lásd még: [A csatlakoztatott gyárspecifikus gyakori kérdések](iot-accelerators-faq-cf.md) és a távoli [figyelés-specifikus gyIK.](iot-accelerators-faq-rm-v2.md)

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerators"></a>Hol találom a megoldásgyorsítók forráskódját?

A forráskód a következő GitHub-tárolókban tárolódik:

* [Távfigyelési megoldásgyorsító (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Távfigyelési megoldásgyorsító (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)
* [Prediktív karbantartási megoldásgyorsító](https://github.com/Azure/azure-iot-predictive-maintenance)
* [Csatlakoztatott gyári megoldásgyorsító](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-sdks-can-i-use-to-develop-device-clients-for-the-solution-accelerators"></a>Milyen SDK-k segítségével fejleszthetem ki az eszközügyfeleket a megoldásgyorsítókhoz?

A [Microsoft Azure IoT SDKS](https://github.com/Azure/azure-iot-sdks) GitHub-adattárakban megtalálhatja a különböző nyelvre (C, .NET, Java, Node.js, Python) IoT-eszköz SDK-kra mutató hivatkozásokat.

Ha a DevKit-eszközt használja, az [IoT DevKit SDK](https://github.com/Microsoft/devkit-sdk) GitHub-tárházban megtalálhatja az erőforrásokat és a mintákat.

### <a name="is-the-new-microservices-architecture-available-for-all-the-three-solution-accelerators"></a>Az új mikroszolgáltatások architektúrája mindhárom megoldásgyorsítóhoz elérhető?

Jelenleg csak a távoli figyelési megoldás használja a mikroszolgáltatások architektúráját, mivel a legszélesebb forgatókönyvet fedi le.

### <a name="what-advantages-does-the-new-open-sourced-microservices-based-architecture-provide-in-the-new-update"></a>Milyen előnyökkel jár az új nyílt forráskódú mikroszolgáltatásokon alapuló architektúra az új frissítésben?

Az elmúlt két évben a felhőarchitektúra jelentősen fejlődött. Mikroszolgáltatások alakultak ki, mint egy nagy minta a skála és a rugalmasság elérése, a fejlesztési sebesség feláldozása nélkül. Ezt az architekturális mintát számos Microsoft-szolgáltatás használja belsőleg, nagy megbízhatósággal és méretezhetőségi eredményekkel. A Microsoft ezeket a tanulságokat a megoldásgyorsítókban helyezi át a gyakorlatba, hogy az ügyfelek élvezhessék azok előnyeit.

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-azure-ad-tenant-how-do-i-complete-this-task"></a>Szolgáltatás-rendszergazda vagyok, és szeretném módosítani a címtár-hozzárendelést az előfizetésem és egy adott Azure AD-bérlő között. Hogyan fejezhetem be ezt a feladatot?

Lásd: [Meglévő előfizetés hozzáadása az Azure AD-címtárhoz](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md#to-associate-an-existing-subscription-to-your-azure-ad-directory)

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organizational-account"></a>Szervezeti fiókkal bejelentkezve szolgáltatás-rendszergazdát vagy társadminisztrátort szeretnék módosítani

Tekintse meg a [Szolgáltatásadminisztrátor és társrendszergazda módosítása szervezeti fiókkal bejelentkezve](https://azure.microsoft.com/support/changing-service-admin-and-co-admin)című támogatási cikket.

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>Miért jelenik meg ez a hiba? "Fiókja nem rendelkezik a megfelelő engedélyekkel a megoldás létrehozásához. Érdeklődjön a fiók rendszergazdájánanál, vagy próbálkozzon egy másik fiókkal."

Az alábbi ábrán útmutatást talál:

![Engedélyek folyamatábrája](media/iot-accelerators-faq/flowchart.png)

> [!NOTE]
> Ha továbbra is megjelenik a hiba érvényesítése után Ön az Azure AD-bérlő globális rendszergazdája és az előfizetés társrendszergazdája, a fiók rendszergazdájával távolítsa el a felhasználót, és rendelje hozzá újra a szükséges engedélyeket ebben a sorrendben. Először adja hozzá a felhasználót globális rendszergazdaként, majd adja hozzá a felhasználót az Azure-előfizetés társadminisztrátoraként. Ha a probléma továbbra is fennáll, forduljon [& súgóhoz.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>Miért jelenik meg ez a hiba, ha Azure-előfizetésem van? "Egy Azure-előfizetés szükséges az előre konfigurált megoldások létrehozásához. Néhány perc alatt létrehozhat egy ingyenes próbafiókot."

Ha biztos benne, hogy rendelkezik egy Azure-előfizetéssel, ellenőrizze az előfizetés bérlői leképezését, és ellenőrizze, hogy a megfelelő bérlő van-e kiválasztva a legördülő menüben. Ha érvényesítette a bérlő helyes, kövesse az előző ábrát, és érvényesítse az előfizetés és az Azure AD-bérlő leképezése.

### <a name="where-can-i-find-information-about-the-previous-version-of-the-remote-monitoring-solution"></a>Hol találhatok információt a távfigyelési megoldás előző verziójáról?

A távoli figyelési megoldásgyorsító korábbi verziója az IoT Suite távoli figyelési előre konfigurált megoldásnéven volt ismert. Az archivált dokumentációt [https://docs.microsoft.com/previous-versions/azure/iot-suite/](https://docs.microsoft.com/previous-versions/azure/iot-suite/)a következő helyen találja: a.

### <a name="is-the-new-solution-accelerator-available-in-the-same-geographic-region-as-the-existing-solution"></a>Az új megoldásgyorsító ugyanabban a földrajzi régióban érhető el, mint a meglévő megoldás?

Igen, az új távoli figyelés ugyanazokat a földrajzi régiókban érhető el.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-solution-accelerator-in-azureiotsolutionscom"></a>Mi a különbség az Azure Portalon lévő erőforráscsoport törlése és a azureiotsolutions.com megoldásgyorsító törlésre való kattintása között?

* Ha törli a megoldásgyorsítót [a azureiotsolutions.com,](https://www.azureiotsolutions.com/)akkor a megoldásgyorsító létrehozásakor üzembe helyezett összes erőforrást törli. Ha további erőforrásokat adott hozzá az erőforráscsoporthoz, ezek az erőforrások is törlődnek.
* Ha törli az erőforráscsoportot az [Azure Portalon,](https://portal.azure.com)csak az adott erőforráscsoport ban lévő erőforrásokat törli. A megoldásgyorsítóhoz társított Azure Active Directory-alkalmazást is törölnie kell.

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-solution-accelerators"></a>Továbbra is hasznosíthatom az Azure IoT-megoldásgyorsítókat?

Igen. A ma létező bármely megoldás továbbra is működik az Azure-előfizetésében, és a forráskód elérhető marad a GitHubon.

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Hány IoT Hub-példányt építhetek ki egy előfizetésben?

Alapértelmezés szerint [előfizetésenként 10 IoT-központot](../azure-resource-manager/management/azure-subscription-service-limits.md#iot-hub-limits)építhet ki. Létrehozhat egy [Azure támogatási jegyet](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a korlát növeléséhez. Ennek eredményeképpen minden megoldásgyorsító egy új IoT Hubot rendel, egy adott előfizetésben legfeljebb 10 megoldásgyorsítót tud kiépíteni.

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>Hány Azure Cosmos DB-példányt tudok kiépíteni egy előfizetésben?

Ötven. Létrehozhat egy [Azure-támogatási jegyet](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a korlát növeléséhez, de alapértelmezés szerint csak 50 Cosmos DB-példányt építhet ki előfizetésenként.

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>Hány ingyenes Bing Térképek API-t adhatok meg egy előfizetésben?

Kettőt. Azure-előfizetésben csak két belső tranzakció1-es szintű Bing Maps for Enterprise-csomag hozhat létre. A távoli figyelési megoldás alapértelmezés szerint ki van építve a Belső tranzakciók 1. Ennek eredményeképpen legfeljebb két távoli figyelési megoldás építhet ki egy előfizetésben módosítások nélkül.

### <a name="can-i-create-a-solution-accelerator-if-i-have-microsoft-azure-for-dreamspark"></a>Létrehozhatok megoldásgyorsítót, ha rendelkezem a Microsoft Azure for DreamSpark szolgáltatással?

> [!NOTE]
> A Microsoft Azure for DreamSpark ma már Microsoft Imagine néven ismert diákok számára.

Jelenleg nem hozhat létre megoldásgyorsítót [egy Microsoft Azure for DreamSpark-fiókkal.](https://azure.microsoft.com/pricing/member-offers/imagine/) Azonban néhány perc alatt létrehozhat egy [ingyenes próbafiókot az Azure-hoz,](https://azure.microsoft.com/free/) amely lehetővé teszi a megoldásgyorsító létrehozását.

### <a name="how-do-i-delete-an-azure-ad-tenant"></a>Hogyan törölhetek egy Azure AD-bérlőt?

Tekintse meg Eric Golpe blogbejegyzését [az Azure AD-bérlő törléséről.](https://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx)

### <a name="next-steps"></a>További lépések

Megismerheti az IoT-megoldásgyorsítók egyéb szolgáltatásait és funkcióit is:

* [A távfigyelési megoldásgyorsító képességeinek megismerése](quickstart-remote-monitoring-deploy.md)
* [A prediktív karbantartási megoldásgyorsító áttekintése](iot-accelerators-predictive-overview.md)
* [Csatlakoztatott gyári megoldásgyorsító telepítése](quickstart-connected-factory-deploy.md)
* [IoT-biztonság létrehozása az alapoktól](/azure/iot-fundamentals/iot-security-ground-up)
