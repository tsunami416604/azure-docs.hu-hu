---
title: Azure működési biztonsági ellenőrzőlistája| Microsoft dokumentumok
description: Ez a cikk az Azure működési biztonságának ellenőrzőlistáját tartalmazza.
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: tomsh
ms.openlocfilehash: fb940857a0f88590cb9bbbf56b9e6a791299309f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75980960"
---
# <a name="azure-operational-security-checklist"></a>Az Azure működési biztonsági ellenőrzőlistája
Egy alkalmazás üzembe helyezése az Azure-ban gyors, egyszerű és költséghatékony. A felhőalapú alkalmazás éles környezetben való üzembe helyezése előtt hasznos, hogy egy ellenőrzőlistát, amely segít kiértékelni az alkalmazás egy listát az alapvető és ajánlott működési biztonsági műveletek, amelyeket figyelembe kell vennie.

## <a name="introduction"></a>Bevezetés

Az Azure olyan infrastruktúra-szolgáltatások csomagját biztosítja, amelyek segítségével telepítheti alkalmazásait. Az Azure Operational Security a felhasználók számára elérhető szolgáltatásokra, vezérlőkre és funkciókra vonatkozik adataik, alkalmazásaik és egyéb eszközeik védelmére a Microsoft Azure-ban.

-   A felhőplatform maximális előnyének kihasználásához azt javasoljuk, hogy használja ki az Azure-szolgáltatásokat, és kövesse az ellenőrzőlistát.
-   Szervezetek, hogy fektessenek időt és erőforrásokat értékeli a működési készültség az alkalmazások elindítása előtt sokkal nagyobb az elégedettség, mint azok, akik nem. A munka végrehajtásasorán az ellenőrzőlisták felbecsülhetetlen mechanizmust jelentenek annak biztosítására, hogy az alkalmazások értékelése következetesen és holisztikusan legyen kiértékelve.
-   Az operatív értékelés szintje a szervezet felhőbeli érettségi szintjétől és az alkalmazás fejlesztési fázisától, a rendelkezésre állási igényektől és az adatérzékenységi követelményektől függően változik.

## <a name="checklist"></a>Ellenőrzőlista

Ez az ellenőrzőlista célja, hogy segítse a vállalatokat a különböző működési biztonsági szempontok átgondolásában, miközben kifinomult vállalati alkalmazásokat telepítenek az Azure-ban. Arra is használható, hogy biztonságos felhőmigrálási és üzemeltetési stratégiát hozzon létre a szervezet számára.

|Ellenőrzőlista kategória| Leírás|
| ------------ | -------- |
| [<br>Biztonsági szerepkörök & a hozzáférés-vezérléshez](../../security-center/security-center-planning-and-operations-guide.md)|<ul><li>[A szerepköralapú hozzáférés-vezérlés (RBAC)](../../role-based-access-control/role-assignments-portal.md) segítségével olyan felhasználóspecifikus, amely adott hatókörben lévő felhasználókhoz, csoportokhoz és alkalmazásokhoz rendelt engedélyeket.</li></ul> |
| [<br>Adatgyűjtés & tárolása](../../storage/blobs/security-recommendations.md)|<ul><li>A Felügyeleti sík biztonsága segítségével a [szerepköralapú hozzáférés-vezérlés (RBAC)](../../role-based-access-control/role-assignments-portal.md)használatával biztosíthatja tárfiókját.</li><li>Adatsík-biztonság az adatokhoz való hozzáférés védelméhez [megosztott hozzáférésű aláírások (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md) és tárolt hozzáférési házirendek használatával.</li><li>Átviteli szintű titkosítás használata – HTTPS és az [SMB (kiszolgálói üzenetblokk protokollok) 3.0](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) által az [Azure fájlmegosztásokhoz](../../storage/files/storage-dotnet-how-to-use-files.md)használt titkosítás használata.</li><li>[Az ügyféloldali titkosítás](../../storage/common/storage-client-side-encryption.md) sal biztosíthatja a tárfiókokba küldött adatokat, ha a titkosítási kulcsok kizárólagos vezérlésére van szükség. </li><li>[A Storage Service Encryption (SSE)](../../storage/common/storage-service-encryption.md) használatával automatikusan titkosítja az adatokat az Azure Storage- ban, az Azure Disk [Encryption](../azure-security-disk-encryption-overview.md) pedig az operációs rendszer és az adatlemezek virtuálisgép-lemezfájljait.</li><li>Az Azure [Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/storage-analytics) segítségével figyelheti az engedélyezési típust; a Blob Storage-hoz hasonlóan láthatja, hogy a felhasználók használták-e a megosztott hozzáférésű aláírást vagy a tárfiók kulcsait.</li><li>Az [Eredetközi erőforrás-megosztás (CORS)](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) használatával különböző tartományokból származó tárolási erőforrásokeléréséhez.</li></ul> |
|[<br>Biztonsági házirendek & javaslatok](../../security-center/security-center-planning-and-operations-guide.md)|<ul><li>Az [Azure Security Center](../../security-center/security-center-install-endpoint-protection.md) használatával végpontmegoldásokat telepíthet.</li><li>A webalkalmazások védelméhez adjon hozzá [egy webalkalmazás-tűzfalat (WAF).](../../application-gateway/waf-overview.md)</li><li>   A biztonsági védelem növeléséhez használjon Microsoft-partnertől származó [tűzfalat.](../../sentinel/connect-data-sources.md) </li><li>Az Azure-előfizetés biztonsági kapcsolattartási adatainak alkalmazása; ez a [Microsoft Security Response Center (MSRC)](https://technet.microsoft.com/security/dn528958.aspx) kapcsolatba lép Önvel, ha azt észleli, hogy az ügyféladatokhoz egy jogellenes vagy jogosulatlan fél fért hozzá.</li></ul> |
| [<br>Identitás & hozzáférés-kezelés](identity-management-best-practices.md)|<ul><li>[Szinkronizálja a helyszíni könyvtárat a felhőkönyvtárral az Azure AD használatával.](../../active-directory/hybrid/whatis-hybrid-identity.md)</li><li>Az Egyszeri bejelentkezés használatával engedélyezheti a felhasználóknak, hogy az Azure [AD-ben](https://azure.microsoft.com/resources/videos/overview-of-single-sign-on/) lévő szervezeti fiókjuk alapján hozzáférjenek saas-alkalmazásaikhoz.</li><li>A [jelszó-visszaállítási regisztrációs tevékenység](../../active-directory/active-directory-passwords-reporting.md) jelentés segítségével figyelheti a regisztráló felhasználókat.</li><li>[Többtényezős hitelesítés (MFA)](../../active-directory/authentication/multi-factor-authentication.md) engedélyezése a felhasználók számára.</li><li>A fejlesztők biztonságos identitáskezelési lehetőségeket használhatnak olyan alkalmazásokhoz, mint a [Microsoft Security Development Lifecycle (SDL).](https://www.microsoft.com/download/details.aspx?id=12379)</li><li>Az Azure AD Premium anomáliajelentései és az [Azure AD-identitásvédelmi képesség](../../active-directory/identity-protection/overview.md)használatával aktívan figyelheti a gyanús tevékenységeket.</li></ul> |
|[<br>Folyamatos biztonsági figyelés](../../security-center/security-center-planning-and-operations-guide.md)|<ul><li>A Malware Assessment Solution [Azure Monitor naplóival](../../log-analytics/log-analytics-queries.md) jelentést készít az infrastruktúra kártevőirtó védelem állapotáról.</li><li>[A Frissítésfelmérés segítségével](../../automation/automation-update-management.md) meghatározhatja a lehetséges biztonsági problémáknak való általános kitettséget, valamint azt, hogy ezek a frissítések kritikusak-e a környezetszámára, vagy ha annak megfelelően.</li><li>Az [Identitás és hozzáférés](../../security-center/security-center-monitoring.md) áttekintést nyújt a felhasználók </li><ul><li>felhasználói identitás állapota,</li><li>a sikertelen bejelentkezési kísérletek száma,</li><li> a felhasználók fiókját, amelyet a kísérletek során használtak, a zárolt fiókokat</li> <li>módosított vagy jelszóalaphelyzetbe állított fiókok </li><li>Jelenleg a bejelentkezett fiókok száma.</li></ul></ul> |
| [<br>Az Azure Security Center észlelési képességei](../../security-center/security-center-alerts-overview.md#detect-threats)|<ul><li>Az [észlelési lehetőségek](../../security-center/security-center-alerts-overview.md#detect-threats)segítségével azonosíthatja a Microsoft Azure-erőforrásokat célzó aktív fenyegetéseket.</li><li>Olyan [integrált fenyegetésfelderítési adatokat](https://blogs.msdn.microsoft.com/azuresecurity/2016/12/19/get-threat-intelligence-reports-with-azure-security-center/) használjon, amelyek ismert rossz szereplőket keresnek a Microsoft termékeiből és szolgáltatásaiból, a [Microsoft Digitális bűncselekmények osztályáról (DCU),](https://www.microsoft.com/trustcenter/security/cybercrime)a [Microsoft Security Response Centerről (MSRC)](response-center.md)és a külső hírcsatornákról származó globális fenyegetésfelderítés kihasználásával.</li><li>Használja az ismert mintákat [alkalmazó viselkedési elemzéseket](https://blogs.technet.microsoft.com/enterprisemobility/2016/06/30/ata-behavior-analysis-monitoring/) a rosszindulatú viselkedés felderítéséhez. </li><li>Használjon [anomáliadetektálást,](https://msdn.microsoft.com/library/azure/dn913096.aspx) amely statisztikai profilkészítést használ egy előzményalap létrehozásához.</li></ul> |
| [<br>Fejlesztői műveletek (DevOps)](https://docs.microsoft.com/azure/architecture/checklist/dev-ops)|<ul><li>[Az Infrastructure as Code (IaC)](https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/) egy olyan gyakorlat, amely lehetővé teszi a hálózatok és virtuális gépek létrehozásának és lebontásának automatizálását és érvényesítését, hogy segítsen a biztonságos, stabil alkalmazásüzemeltetési platformok biztosításában.</li><li>[A folyamatos integráció és üzembe helyezés](/visualstudio/containers/overview#continuous-delivery-and-continuous-integration-cicd) a kód folyamatos egyesítését és tesztelését hajtja, ami a hibák korai megtalálásához vezet. </li><li>[Kiadáskezelése](https://msdn.microsoft.com/library/vs/alm/release/overview) Automatizált központi telepítések kezelése a folyamat minden egyes szakaszán.</li><li>A futó alkalmazások [alkalmazásteljesítmény-figyelése,](https://azure.microsoft.com/documentation/articles/app-insights-start-monitoring-app-health-usage/) beleértve az alkalmazásállapotának és az ügyfélhasználatnak szóló éles környezeteket, segít a szervezeteknek egy hipotézis tanait létrehozni, és gyorsan érvényesíteni vagy megcáfolni a stratégiákat.</li><li>A [Terheléstesztelés & automatikus méretezése](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing) segítségével teljesítményproblémákat találhatunk az alkalmazásunkban a telepítés minőségének javítása és annak biztosítása érdekében, hogy alkalmazásunk mindig rendelkezésre áll vagy elérhető legyen az üzleti igények kielégítésére.</li></ul> |


## <a name="conclusion"></a>Összegzés
Számos szervezet sikeresen telepítette és üzemeltette felhőalapú alkalmazásait az Azure-ban. A megadott ellenőrzőlisták számos olyan ellenőrző listát emelnek ki, amelyek elengedhetetlenek, és segítenek a sikeres telepítések és a frusztrációmentes műveletek valószínűségének növelésében. Ezeket a működési és stratégiai szempontokat az Azure-beli meglévő és új alkalmazás-üzembe helyezésekhez ajánljuk.

## <a name="next-steps"></a>További lépések
A biztonságról az alábbi cikkekben olvashat bővebben:

- [Tervezés és üzembiztonság](https://www.microsoft.com/trustcenter/security/designopsecurity).
- [Az Azure Security Center tervezése és műveletei.](../../security-center/security-center-planning-and-operations-guide.md)
