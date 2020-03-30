---
title: Biztonsági javaslatok
description: Hajtsa végre a biztonsági javaslatokat, hogy segítsen teljesíteni a biztonsági kötelezettségeit, ahogy azt a közös felelősség modellünk is szerepel. Az alkalmazás biztonságának növelése.
author: barclayn
manager: barbkess
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: barclayn
ms.custom: security-recommendations
ms.openlocfilehash: e3f901fd78436b42729607d38f0ddb533f31cd30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74684112"
---
# <a name="security-recommendations-for-app-service"></a>Az App Service biztonsági javaslatai

Ez a cikk az Azure App Service biztonsági javaslatait tartalmazza. Ezeknek a javaslatoknak a megvalósításában segít teljesíteni a közös felelősségi modellünkben leírt biztonsági kötelezettségeit, és javítja a Web App-megoldások általános biztonságát. Ha többet szeretne tudni arról, hogy a Microsoft mit tesz a szolgáltatói feladatok teljesítése érdekében, olvassa el az [Azure infrastruktúra-biztonságát.](../security/fundamentals/infrastructure.md)

## <a name="general"></a>Általános kérdések

| Ajánlás | Megjegyzések |
|-|-|----|
| Legyen naprakész | A támogatott platformok, programozási nyelvek, protokollok és keretrendszerek legújabb verzióit használhatja. |

## <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Ajánlás | Megjegyzések |
|-|----|
| Névtelen hozzáférés letiltása | Hacsak nem kell támogatnia a névtelen kéréseket, tiltsa le a névtelen hozzáférést. Az Azure App Service hitelesítési beállításairól további információt a [Hitelesítés és engedélyezés az Azure App Service szolgáltatásban című témakörben talál.](overview-authentication-authorization.md)|
| Hitelesítés megkövetelése | Amikor csak lehetséges, használja az App Service hitelesítési modul helyett a kód írása a hitelesítés és engedélyezés kezelésére. Lásd: [Hitelesítés és engedélyezés az Azure App Service szolgáltatásban.](overview-authentication-authorization.md) |
| Háttérerőforrások védelme hitelesített hozzáféréssel | Használhatja a felhasználó identitását, vagy egy alkalmazásidentitást használhat a háttér-erőforrás hitelesítéséhez. Ha úgy dönt, hogy egy alkalmazásidentitás használata egy [felügyelt identitást.](overview-managed-identity.md)
| Ügyféltanúsítvány-hitelesítés megkövetelése | Az ügyféltanúsítvány-hitelesítés növeli a biztonságot azáltal, hogy csak olyan ügyfelek kapcsolatait engedélyezi, amelyek az Ön által megadott tanúsítványokkal hitelesíthetők. |

## <a name="data-protection"></a>Adatvédelem

| Ajánlás | Megjegyzések |
|-|-|
| HTTP átirányítása a HTTT-kre | Alapértelmezés szerint az ügyfelek http vagy HTTPS használatával is csatlakozhatnak a webalkalmazásokhoz. Javasoljuk, hogy a HTTP-t átirányítsa a HTTPs-be, mert a HTTPS az SSL/TLS protokollt használja a biztonságos kapcsolat biztosításához, amely titkosított és hitelesített is. |
| Az Azure-erőforrásokkal való kommunikáció titkosítása | Amikor az alkalmazás csatlakozik az Azure-erőforrásokhoz, például az [SQL Database](https://azure.microsoft.com/services/sql-database/) vagy az [Azure Storage,](/azure/storage/)a kapcsolat az Azure-ban marad. Mivel a kapcsolat az Azure-ban megosztott hálózatkezelésen keresztül történik, mindig titkosítsa az összes kommunikációt. |
| A lehető legújabb TLS-verzió megkövetelése | 2018 óta az új Azure App Service-alkalmazások a TLS 1.2-t használják. A TLS újabb verziói biztonsági fejlesztéseket tartalmaznak a régebbi protokollverziókhoz. |
| FTPS használata | Az App Service támogatja az FTP és az FTPS szolgáltatást a fájlok telepítéséhez. Ha lehetséges, ftp helyett FTP-t használjon. Ha az egyik vagy mindkét protokoll nincs használatban, [tiltsa le őket.](deploy-ftp.md#enforce-ftps) |
| Az alkalmazásadatok védelme | Ne tárolja az alkalmazástitkos kulcsokat, például az adatbázis hitelesítő adatait, AZ API-jogkivonatokat vagy a titkos kulcsokat a kód- vagy konfigurációs fájlokban. Az általánosan elfogadott megközelítés az, hogy a hozzáférést a [környezeti változók](https://wikipedia.org/wiki/Environment_variable) a szabványos minta az Ön által választott nyelven. Az Azure App Service-ben az [alkalmazásbeállítások](web-sites-configure.md) és a kapcsolati karakterláncok segítségével határozhatja meg a környezeti [változókat.](web-sites-configure.md) Az alkalmazásbeállítások és a kapcsolati karakterláncok az Azure-ban titkosítottan tárolódnak. Az alkalmazás beállításait a program csak akkor fejti vissza, mielőtt az alkalmazás indításakor bekerülne az alkalmazás folyamatmemóriájába. A titkosítási kulcsok rendszeresen elfordulnak. Azt is megteheti, hogy integrálja az Azure App Service-alkalmazást az [Azure Key Vault](/azure/key-vault/) tal a speciális titkos kulcsok kezeléséhez. A [Key Vault felügyelt identitással való elérésével](../key-vault/tutorial-web-application-keyvault.md)az App Service-alkalmazás biztonságosan hozzáférhet a szükséges titkokhoz. |

## <a name="networking"></a>Hálózat

| Ajánlás | Megjegyzések |
|-|-|
| Statikus IP-korlátozások használata | A Windows-alapú Azure App Service lehetővé teszi az alkalmazás eléréséhez engedélyezett IP-címek listájának meghatározását. Az engedélyezett lista tartalmazhat egyedi IP-címeket vagy egy alhálózati maszk által meghatározott IP-címtartományt. További információ: [Azure App Service static IP restrictions](app-service-ip-restrictions.md).  |
| Az elkülönített tarifacsomag használata | Az elkülönített tarifacsomag kivételével az összes réteg futtatja az alkalmazásokat az Azure App Service megosztott hálózati infrastruktúráján. Az elkülönített szint teljes hálózati elkülönítést biztosít az alkalmazások dedikált [App Service-környezetben](environment/intro.md)való futtatásával. Az App Service-környezet az [Azure Virtual Network](/azure/virtual-network/)saját példányában fut.|
| Biztonságos kapcsolatok használata a helyszíni erőforrások elérésekor | [A hibrid kapcsolatok,](app-service-hybrid-connections.md) [a virtuális hálózati integráció](web-sites-integrate-with-vnet.md)vagy az [App Service-környezet](environment/intro.md) segítségével csatlakozhat a helyszíni erőforrásokhoz. |
| A bejövő hálózati forgalom nak való kitettség korlátozása | A hálózati biztonsági csoportok lehetővé teszik a hálózati hozzáférés korlátozását és a kitett végpontok számának szabályozását. További információ: [Inbound Traffic to a App Service Environment](environment/app-service-app-service-environment-control-inbound-traffic.md). |

## <a name="monitoring"></a>Figyelés

| Ajánlás | Megjegyzések |
|-|-|
|Az Azure Security Center standard szintű csomagjának használata | Az [Azure Security Center](../security-center/security-center-app-services.md) natív módon integrálva van az Azure App Service szolgáltatással. Felméréseket futtathat, és biztonsági javaslatokat tehet. |

## <a name="next-steps"></a>További lépések

Érdeklődjön az alkalmazás szolgáltatójánál, hogy vannak-e további biztonsági követelmények. A biztonságos alkalmazások fejlesztéséről a Biztonságos fejlesztési dokumentáció című témakörben olvashat [bővebben.](../security/fundamentals/abstract-develop-secure-apps.md)
