---
title: Biztonsági javaslatok az Azure App Service-ben
description: Biztonsági javaslatok az Azure App Service szolgáltatásban. Ezek az ajánlások megvalósítása biztonsági kötelezettségeinek teljesítéséhez, a közös felelősség modell és a fogja leírtak szerint súgó javulni fog a web app-megoldások általános biztonságát.
services: app-service
author: barclayn
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: barclayn
ms.custom: security-recommendations
ms.openlocfilehash: 53cd2b1dde1158a1c46f798e57911dad110dc87e
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718262"
---
# <a name="security-recommendations-for-app-service"></a>Biztonsági javaslatok az App Service-ben

Ez a cikk tartalmazza a biztonsági javaslatok az Azure App Service-ben. Ezek az ajánlások megvalósítása biztonsági kötelezettségeinek teljesítéséhez, a közös felelősség modell és a fogja leírtak szerint súgó javulni fog a webalkalmazás-megoldások általános biztonságát. A service provider feladatok teljesítése nem a Microsoft milyen további információk, olvassa el a [Azure-infrastruktúra biztonsági](../security/azure-security-infrastructure.md).

## <a name="general"></a>Általános

| Ajánlás | Megjegyzések |
|-|-|----|
| Naprakész | A támogatott platformok, programnyelvek, protokollok és keretrendszereket legfrissebb verzióit használja. |

## <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Ajánlás | Megjegyzések |
|-|----|
| Névtelen hozzáférés letiltása | Ha nincs szükség a névtelen kérelmek támogatásához, tiltsa le a névtelen hozzáférés. További információ az Azure App Service-hitelesítés beállításai: [hitelesítése és engedélyezése Azure App Service-ben](overview-authentication-authorization.md).|
| Hitelesítés megkövetelése | Amikor csak lehetséges, használja az App Service-hitelesítés modulját kód írása helyett kezeli a hitelesítési és engedélyezési. Lásd: [hitelesítése és engedélyezése Azure App Service-ben](overview-authentication-authorization.md). |
| A hitelesített hozzáférést háttér-erőforrások védelme | A felhasználói identitás használatára, vagy Alkalmazásidentitás használják a háttér-erőforráshoz való hitelesítéshez. Ha úgy dönt, használjon egy alkalmazás-identitást használja egy [identitás](overview-managed-identity.md).
| Ügyféltanúsítvány-alapú hitelesítés megkövetelése | Ügyféltanúsítvány-alapú hitelesítés biztonságosabbá teszi a kapcsolatok csak teszi az ügyfelek, amelyek az Ön által megadott tanúsítványok használatával történő hitelesítésnek. |

## <a name="data-protection"></a>Adatvédelem

| Ajánlás | Megjegyzések |
|-|-|
| Redirect HTTP to HTTPs | Alapértelmezés szerint az ügyfelek HTTP- vagy HTTPS használatával kapcsolódhatnak web Apps szolgáltatásban. Javasoljuk, hogy HTTP átirányítása HTTPs, mert a HTTPS az SSL/TLS protokoll használatával biztosítja a biztonságos kapcsolatot, amely mindkét titkosítva, és hitelesíteni. |
| Azure-erőforrásokhoz való kommunikáció titkosításához | Ha az alkalmazás csatlakozik Azure-erőforrások, például [SQL Database](https://azure.microsoft.com/services/sql-database/) vagy [Azure Storage](/azure/storage/), a kapcsolat az Azure-ban marad. A kapcsolat halad végig a megosztott hálózati az Azure-ban, mivel mindig titkosítsa az összes kommunikációt. |
| A legújabb lehetséges TLS-verzió megkövetelése | Új Azure App Service-alkalmazások 2018 óta a TLS 1.2 használatára. A TLS újabb verzióival közé tartozik a biztonsági protokoll régebbi verziókhoz képest. |
| FTPS használata | App Service-ben FTP és a FTPS támogatja a fájlok telepítéséhez. FTPS használata helyett FTP, amikor csak lehetséges. Ha az egyik vagy mindkét ezeket a protokollokat, amelyek nem használja, akkor [őket tiltani](deploy-ftp.md#enforce-ftps). |
| Az alkalmazásadatok védelme | Titkos alkalmazáskulcsok, például az adatbázis hitelesítő adatait, API-jogkivonatai vagy titkos kulcsok ne tároljon a kódot, vagy a konfigurációs fájlokban. Az általánosan elfogadott megközelítés érheti el őket, mint a következő [környezeti változók](https://wikipedia.org/wiki/Environment_variable) tetszőleges nyelven a szokásos mintájának használatával. Az Azure App Service-ben, megadhatja a környezeti változókat [Alkalmazásbeállítások](web-sites-configure.md) és [kapcsolati karakterláncok](web-sites-configure.md). Alkalmazásbeállítások és a kapcsolati karakterláncok az Azure-ban titkosítva tárolódnak. A beállítások lesznek visszafejtve előtt csak folyamatban kártevő program férkőzik be az alkalmazás folyamata memória az alkalmazás indításakor. A titkosítási kulcsokat a rendszer rendszeresen elforgatva. Másik lehetőségként integrálható az Azure App Service alkalmazás- [Azure Key Vault](/azure/key-vault/) speciális titkos kulcsok kezeléséhez. Által [felügyelt identitással a Key Vault elérése](../key-vault/tutorial-web-application-keyvault.md), az App Service-alkalmazást biztonságosan férhet hozzá a titkos kulcsok van szüksége. |

## <a name="networking"></a>Hálózat

| Ajánlás | Megjegyzések |
|-|-|
| Statikus IP-korlátozások használata | A Windows Azure App Service lehetővé teszi, hogy az alkalmazás-hozzáférést IP-címek listájának meghatározását. Az engedélyezett listára egyedi IP-címek hozzáadhatja, vagy egy IP-címtartomány határozzák meg egy alhálózati maszkot. További információkért lásd: [Azure App Service statikus IP-korlátozások](app-service-ip-restrictions.md).  |
| Az elkülönített tarifacsomag használata | Az elkülönített tarifacsomag kivételével minden szinten futtassa alkalmazásait az Azure App Service-ben a megosztott hálózati infrastruktúrán. Az izolált csomag lehetővé teszi teljes hálózatelkülönítés az alkalmazásokat egy dedikált futtatásával [App Service Environment-környezet](environment/intro.md). App Service-környezet fut, a saját példányát [Azure Virtual Network](/azure/virtual-network/).|
| Biztonságos kapcsolatot használ, amikor fér hozzá a helyszíni erőforrások | Használhat [hibrid kapcsolatok](app-service-hybrid-connections.md), [virtuális hálózati integráció](web-sites-integrate-with-vnet.md), vagy [App Service-környezet](environment/intro.md) a helyszíni erőforrások eléréséhez. |
| Korlátozza a bejövő hálózati forgalom | Hálózati biztonsági csoportok lehetővé teszik a hálózati hozzáférés korlátozása, és szabályozhatja az elérhető végpontok száma. További információkért lásd: [hogyan a bejövő forgalom szabályozása az App Service-környezet](environment/app-service-app-service-environment-control-inbound-traffic.md). |

## <a name="monitoring"></a>Figyelés

| Ajánlás | Megjegyzések |
|-|-|
|Az Azure Security Center standard szintű csomag | [Az Azure Security Center](../security-center/security-center-app-services.md) natív módon integrálva van az Azure App Service-ben. Ez vizsgálatokat futtatja, és adja meg a biztonsági javaslatok. |

## <a name="next-steps"></a>További lépések

Ellenőrizze az alkalmazás-szolgáltatót, hogy vannak-e további biztonsági követelményeket. Biztonságos alkalmazások fejlesztéséről további információkért lásd: [biztonságos fejlesztési dokumentáció](../security/abstract-develop-secure-apps.md).
