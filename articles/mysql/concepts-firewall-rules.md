---
title: "Azure-adatbázis a MySQL-kiszolgáló tűzfalszabályainak |} Microsoft Docs"
description: "A témakör ismerteti a tűzfalszabályok a MySQL-kiszolgálóhoz tartozó Azure-adatbázis."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 1/20/2018
ms.openlocfilehash: 3cc879fb568965af98997889437c6348e0d341e1
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2018
---
# <a name="azure-database-for-mysql-server-firewall-rules"></a>Kiszolgáló tűzfalszabályainak MySQL Azure-adatbázis
Tűzfalak tagadni a hozzáférést minden az adatbázis-kiszolgáló csak akkor adja meg, mely számítógépek rendelkeznek engedéllyel. A tűzfal engedélyezi a hozzáférést a kiszolgálóhoz, az egyes kérelmek az eredeti IP-címe alapján.

Beállítani a tűzfalat, hozzon létre tűzfalszabályokat, amelyek elfogadható IP-címek tartományát adja meg. Tűzfal-szabályokat hozhat létre a kiszolgálói szinten.

**Tűzfal-szabályok:** ezek a szabályok hozzáférés engedélyezése ügyfelek számára a teljes Azure-adatbázis MySQL-kiszolgáló esetén ez azt jelenti, hogy összes adatbázis belül az azonos logikai kiszolgáló. Kiszolgálószintű tűzfal-szabályokat az Azure-portálon vagy az Azure parancssori felület parancsait használatával konfigurálható. Kiszolgálószintű tűzfal-szabályok létrehozása, az előfizetés tulajdonosa vagy egy előfizetés közreműködői kell lennie.

## <a name="firewall-overview"></a>Tűzfal áttekintése
Az adatbázis elérésére az Azure-adatbázis MySQL-kiszolgáló a tűzfal blokkolja alapértelmezés szerint ki van. A kiszolgáló egy másik számítógépről használatának megkezdéséhez meg kell adnia egy vagy több kiszolgálószintű engedélyezésére szolgáló tűzfalszabályok a kiszolgáló elérését. Használja a tűzfalszabályok adja meg, melyik IP-címtartományok engedélyezi az internetről. A tűzfalszabályok nincs hatással a hozzáférést az Azure portál magát.

Kapcsolódási kísérletek az internetről és az Azure először eltelik a tűzfalon keresztül azok képes elérni az Azure-adatbázis MySQL-adatbázis, az alábbi ábrán látható módon:

![A tűzfal működése áramló – példa](./media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>Csatlakozás az internetről
Az Azure-adatbázishoz a MySQL-kiszolgálón lévő összes adatbázis kiszolgálószintű tűzfal-szabályokat alkalmazni.

Ha az IP-cím, a kérelem megadott kiszolgálószintű tűzfalszabályok tartományok egyikébe esik, akkor a rendszer engedélyezi a csatlakozást.

Ha a kérés IP-címét az adatbázis-szintjére, vagy a kiszolgálószintű tűzfalszabály megadott tartományok kívül, akkor a kapcsolódási kérelem sikertelen lesz.

## <a name="connecting-from-azure"></a>Csatlakozás az Azure-ból
Lehetővé teszik az alkalmazások az Azure-ból a MySQL-kiszolgáló Azure-adatbázishoz való kapcsolódáshoz, Azure-kapcsolatok engedélyezni kell. Például egy Azure Web Apps alkalmazást vagy olyan alkalmazás, amely egy Azure virtuális gép üzemeltetésére, vagy csatlakoztassa egy Azure Data Factory az adatkezelési átjáró. Az erőforrások nem kell ugyanazon virtuális hálózatot (VNET) vagy a tűzfalszabály tartozó erőforráscsoport ahhoz, hogy ezeket a kapcsolatokat. Amikor egy Azure-alkalmazás megkísérel csatlakozni az adatbázis-kiszolgálóhoz, a tűzfal ellenőrzi, hogy az Azure-kapcsolatok engedélyezve vannak-e. Többféle módszer ahhoz, hogy ilyen típusú kapcsolatokat. A 0.0.0.0 kezdő- és zárócímet tartalmazó tűzfalbeállítás jelzi, hogy ezek a kapcsolatok engedélyezettek. Beállíthatja azt is megteheti, a **Azure-szolgáltatásokhoz való hozzáférés engedélyezése** lehetőséggel **ON** a portálon a **kapcsolatbiztonsági** ablaktáblán, és nyomja le **mentése**. A kapcsolódási kísérlet nem engedélyezett, ha a kérelem nem éri el a MySQL-kiszolgálóhoz tartozó Azure-adatbázis.

> [!IMPORTANT]
> Ez a beállítás konfigurálja a tűzfalat arra, hogy engedélyezzen minden, az Azure felől érkező kapcsolatot, beleértve a más ügyfelek előfizetéseiből érkező kapcsolatokat is. Ezen beállítás kiválasztásakor győződjön meg arról, hogy a bejelentkezési és felhasználói engedélyei a hozzáféréseket az arra jogosult felhasználókra korlátozzák.
> 

![Azure-szolgáltatásokhoz való hozzáférés engedélyezése a portál konfigurálása](./media/concepts-firewall-rules/allow-azure-services.png)

## <a name="programmatically-managing-firewall-rules"></a>Tűzfalszabályok szoftveres kezelése
Az Azure portálon kívül tűzfalszabályok kezelhető programozott módon az Azure parancssori felület használatával. Lásd még: [hozzon létre és kezelheti az Azure-adatbázis MySQL tűzfalszabályok Azure parancssori felület használatával](./howto-manage-firewall-using-cli.md)

## <a name="troubleshooting-the-database-firewall"></a>Az adatbázistűzfal hibaelhárítása
Ha a Microsoft Azure-adatbázis eléréséhez a MySQL-kiszolgáló szolgáltatás nem a várt módon működnek, vegye figyelembe a következő szempontokat:

* **Az engedélyezési listán módosításai nem lépnek érvénybe még:** lehet, mint amennyit egy 5 perces késleltetést használ az vált, az Azure-adatbázishoz a MySQL-kiszolgáló tűzfal konfigurációjának érvénybe léptetéséhez.

* **A bejelentkezés nem engedélyezett, vagy helytelen jelszót használt:** egy bejelentkezési azonosító nem rendelkezik engedélyekkel a MySQL-kiszolgálóhoz tartozó Azure-adatbázis, vagy a használt jelszó nem megfelelő, ha a kapcsolat a MySQL-kiszolgálóhoz tartozó Azure-adatbázis megtagadva. Egy tűzfalbeállítás létrehozása lehetővé teszi az ügyfelek számára a kiszolgálóhoz való csatlakozás megkísérlését, azonban minden egyes ügyfélnek meg kell adnia a szükséges biztonsági hitelesítő adatokat.

* **Dinamikus IP-cím:** Ha az internethez, a dinamikus IP-címzést és tapasztal első a tűzfalon keresztül, próbálja meg a következő megoldásoknak a valamelyikét:

* Az internetszolgáltató (ISP) kérjen a MySQL-kiszolgálóhoz tartozó Azure-adatbázis elérő ügyfélszámítógépeken rendelt IP-címtartományt, és adja hozzá az IP-címtartományt, egy tűzfalszabályt.

* Állítson be statikus IP-címeket az ügyfélszámítógépei számára, majd adja meg az IP-címeket tűzfalszabályokként.

## <a name="next-steps"></a>További lépések

[Hozzon létre és kezelheti az Azure-adatbázis MySQL tűzfalszabályokat az Azure portál használatával](./howto-manage-firewall-using-portal.md)
[hozzon létre és kezelheti az Azure-adatbázis MySQL tűzfalszabályok Azure parancssori felület használatával](./howto-manage-firewall-using-cli.md)
