---
title: Azure Database for MySQL-kiszolgáló tűzfalszabályait
description: A témakör ismerteti a tűzfalszabályok az Azure Database for MySQL-kiszolgálóhoz.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: a7016b8ca43abee9c3f346c6dec55a101ce4020a
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53541217"
---
# <a name="azure-database-for-mysql-server-firewall-rules"></a>Azure Database for MySQL-kiszolgáló tűzfalszabályait
Tűzfalak mindaddig megakadályozzák az adatbázis-kiszolgálóhoz való hozzáférés teljes, csak akkor adja meg, hogy mely számítógépek rendelkeznek ehhez engedéllyel. A tűzfal hozzáférést biztosít a kiszolgáló minden kérés eredeti IP-címe alapján.

Konfigurálja a tűzfalat, hozzon létre tűzfalszabályokat, amelyek elfogadható IP-címek tartományát adja meg. A kiszolgálószintű tűzfalszabályok is létrehozhat.

**Tűzfal-szabályok:** Ezek a szabályok hozzáférést biztosítanak ügyfelek számára a teljes Azure Database for MySQL-kiszolgáló, azaz lévő összes adatbázis az egyazon logikai kiszolgálón. Kiszolgálószintű tűzfalszabályok az Azure portal vagy az Azure CLI-parancsok használatával konfigurálható. Kiszolgálószintű tűzfalszabályok létrehozásához, az előfizetés tulajdonosa vagy az előfizetés közreműködői kell lennie.

## <a name="firewall-overview"></a>Tűzfal áttekintése
Minden adatbázis-hozzáférést az Azure Database for MySQL-kiszolgáló blokkolja a tűzfal alapértelmezés szerint ki van. A kiszolgáló egy másik számítógépről használatának megkezdéséhez szüksége adjon meg egy vagy több kiszolgálószintű tűzfalszabályokat a kiszolgáló elérésének engedélyezéséhez. Használja a tűzfal-szabályokat, adja meg, hogy mely IP-címtartományok, hogy az internetről. Az Azure portál maga a hozzáférést a tűzfalszabályok nem befolyásolják.

Kapcsolódási kísérletek az internetről és az Azure először át kell jutniuk a tűzfalon, mielőtt elérnék az Azure Database for MySQL-adatbázis, az alábbi ábrán látható módon:

![A példában a folyamat a tűzfal működéséről](./media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>Csatlakozás az internetről
Kiszolgálószintű tűzfalszabályok az Azure Database for MySQL-kiszolgáló összes adatbázisára vonatkoznak.

Ha a kérés IP-címe a kiszolgálószintű tűzfalszabályok megadott tartományok egyikében található, majd engedélyezi a csatlakozást.

Ha a kérés IP-címe az adatbázisszintű vagy kiszolgálószintű tűzfalszabályok megadott tartományok kívül, akkor a kapcsolódási kérelem sikertelen lesz.

## <a name="connecting-from-azure"></a>Csatlakozás az Azure-ból
Ahhoz, hogy az Azure-alkalmazások az Azure Database for MySQL-kiszolgálóhoz való kapcsolódáshoz, engedélyezni kell az Azure-kapcsolatokat. Ha például üzemeltetése az Azure Web Apps-alkalmazáshoz, vagy egy Azure-beli virtuális gépen futó alkalmazást, vagy egy Azure Data Factory az adatkezelési átjáró csatlakozni. Az erőforrások nem kell ugyanazon a virtuális hálózaton (VNet) vagy a tűzfalszabály erőforráscsoportjának ahhoz, hogy ezeket a kapcsolatokat lehet. Amikor egy Azure-alkalmazás megkísérel csatlakozni az adatbázis-kiszolgálóhoz, a tűzfal ellenőrzi, hogy az Azure-kapcsolatok engedélyezve vannak-e. Van néhány módszerek ilyen típusú kapcsolatok engedélyezéséhez. A 0.0.0.0 kezdő- és zárócímet tartalmazó tűzfalbeállítás jelzi, hogy ezek a kapcsolatok engedélyezettek. Beállíthatja azt is megteheti, a **Azure-szolgáltatásokhoz való hozzáférés engedélyezése** beállítást **ON** a portálon a **kapcsolatbiztonság** ablaktáblán, majd nyomja le **mentése**. A csatlakozási kísérlet nem engedélyezett, ha a kérés nem éri el az Azure Database for MySQL-kiszolgálóhoz.

> [!IMPORTANT]
> Ez a beállítás konfigurálja a tűzfalat arra, hogy engedélyezzen minden, az Azure felől érkező kapcsolatot, beleértve a más ügyfelek előfizetéseiből érkező kapcsolatokat is. Ezen beállítás kiválasztásakor győződjön meg arról, hogy a bejelentkezési és felhasználói engedélyei a hozzáféréseket az arra jogosult felhasználókra korlátozzák.
> 

![Konfigurálja az Azure-szolgáltatásokhoz való hozzáférés engedélyezése a portálon](./media/concepts-firewall-rules/allow-azure-services.png)

## <a name="programmatically-managing-firewall-rules"></a>Tűzfalszabályok szoftveres kezelése
Az Azure Portalon kívül tűzfalszabályok kezelhetők programozott módon az Azure parancssori felület használatával. Lásd még: [hozzon létre és kezelhető az Azure Database for MySQL-tűzfalszabályok Azure CLI használatával](./howto-manage-firewall-using-cli.md)

## <a name="troubleshooting-the-database-firewall"></a>Az adatbázistűzfal hibaelhárítása
Ha a hozzáférés a Microsoft Azure database for MySQL-kiszolgáló szolgáltatás nem a várt módon működnek, vegye figyelembe a következőket:

* **Az engedélyezési lista módosításai nem lépnek érvénybe még:** Előfordulhat, mint öt perces késleltetés módosításait az Azure Database for MySQL-kiszolgáló tűzfal-konfiguráció érvénybe léptetéséhez.

* **A bejelentkezés nem engedélyezett, vagy helytelen jelszó lett megadva:** Ha egy bejelentkezés nem rendelkezik engedélyekkel az Azure Database for MySQL-kiszolgálóhoz, vagy a jelszó helytelen, a rendszer megtagadja a kapcsolatot az Azure database for MySQL-kiszolgáló. Egy tűzfalbeállítás létrehozása lehetővé teszi az ügyfelek számára a kiszolgálóhoz való csatlakozás megkísérlését, azonban minden egyes ügyfélnek meg kell adnia a szükséges biztonsági hitelesítő adatokat.

* **Dinamikus IP-cím:** Ha internetkapcsolat és a dinamikus IP-címkezelés és problémákat okoz a tűzfalon, megpróbálhatja a következő megoldások valamelyikét:

* Az internetszolgáltató (ISP) kérjen a, az Azure Database for MySQL-kiszolgáló eléréséhez használt ügyfélszámítógépeihez társított IP-címtartományt, és hozzáadhatja az IP-címtartományt egy tűzfalszabályként.

* Állítson be statikus IP-címeket az ügyfélszámítógépei számára, majd adja meg az IP-címeket tűzfalszabályokként.

## <a name="next-steps"></a>További lépések

[Hozzon létre és kezelhető az Azure Database for MySQL tűzfalszabályok az Azure portal használatával](./howto-manage-firewall-using-portal.md)
[hozzon létre és kezelhető az Azure Database for MySQL-tűzfalszabályok Azure CLI használatával](./howto-manage-firewall-using-cli.md)
