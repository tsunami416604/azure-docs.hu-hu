---
title: Csatlakozás a meglévő Azure App Service az Azure Database for MySQL-hez
description: Utasítások megfelelően csatlakoztatása egy meglévő Azure App Service az Azure Database for MySQL
services: mysql
author: ajlam
ms.author: andrela
editor: jasonwhowell
manager: kfile
ms.service: mysql
ms.topic: article
ms.date: 09/26/2018
ms.openlocfilehash: 4aecc4941f2181216ea537c0019152ce822ac4b0
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47408935"
---
# <a name="connect-an-existing-azure-app-service-to-azure-database-for-mysql-server"></a>Csatlakozás a meglévő Azure App Service az Azure Database for MySQL-kiszolgáló
Ez a témakör azt ismerteti, hogyan csatlakozhat az Azure Database for MySQL-kiszolgáló egy meglévő Azure App Service.

## <a name="before-you-begin"></a>Előkészületek
Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Hozzon létre egy Azure Database for MySQL-kiszolgálóhoz. Részletekért tekintse meg a [hogyan hozhat létre az Azure Database for MySQL-kiszolgáló portálról](quickstart-create-mysql-server-database-using-azure-portal.md) vagy [hogyan hozhat létre az Azure Database for MySQL-kiszolgáló CLI-vel](quickstart-create-mysql-server-database-using-azure-cli.md).

Jelenleg nincsenek két megoldás MySQL-hez készült Azure-adatbázis az Azure App Service elérésének engedélyezéséhez. A két megoldás magában foglalja a kiszolgálószintű tűzfalszabályok beállítása.

## <a name="solution-1---allow-azure-services"></a>1 - megoldás az Azure-szolgáltatások engedélyezése
Azure Database for MySQL tűzfalat használ az adatok védelme érdekében hozzáférés-biztonságot nyújt. Történő csatlakozás egy Azure App Service az Azure Database for MySQL-kiszolgáló, vegye figyelembe, hogy az App Service kimenő IP-címek dinamikusak jellegűek. Az "Azure-szolgáltatásokhoz való hozzáférés engedélyezése" beállítás lehetővé teszi az app service, a MySQL-kiszolgálóhoz való csatlakozáshoz.

1. Az MySQL server a beállítások panelen kattintson a fejléc **kapcsolatbiztonság** a kapcsolatbiztonság panel megnyitásához az Azure Database for MySQL-hez.

   ![Az Azure portal – kapcsolatbiztonság kattintson](./media/howto-connect-webapp/1-connection-security.png)

2. Válassza ki **ON** a **Azure-szolgáltatásokhoz való hozzáférés engedélyezése**, majd **mentése**.
   ![Az Azure portal – engedélyezi az Azure-hozzáférés](./media/howto-connect-webapp/allow-azure.png)

## <a name="solution-2---create-a-firewall-rule-to-explicitly-allow-outbound-ips"></a>2. megoldás, hozzon létre egy tűzfalszabályt, hogy kifejezetten engedélyezzék a kimenő IP-címek
Minden a kimenő IP-címet az Azure App Service explicit módon adhat hozzá.

1. App Service-tulajdonságok paneljén megtekintheti a **kimenő IP-cím**.

   ![Az Azure portal - nézet kimenő IP-címek](./media/howto-connect-webapp/2_1-outbound-ip-address.png)

2. A MySQL-kapcsolat biztonsági panelen adja hozzá a kimenő IP-címek egyesével.

   ![Az Azure portal - explicit IP-címek hozzáadása](./media/howto-connect-webapp/2_2-add-explicit-ips.png)

3. Ne felejtse el **mentése** a tűzfalszabályokat.

Bár az Azure App Service-ben, hogy állandó IP-címek idővel megpróbálja, nincsenek esetekben, ahol az IP-címek változhat. Például ez akkor fordulhat elő, amikor az alkalmazás újrahasznosítást vagy egy skálázási művelet történik, vagy amikor új számítógépek kerülnek az Azure regionális adatközpontokat növeli a kapacitást. Az IP-címek módosítása, ha az alkalmazás sikerült szoftverkarbantartás abban az esetben, ha már nem képes csatlakozni a MySQL-kiszolgálóhoz. A fenti megoldások kiválasztásakor vegye figyelembe a figyelmet.

## <a name="ssl-configuration"></a>SSL-konfigurációja
Azure Database for MySQL-hez SSL alapértelmezés szerint engedélyezve van. Ha az alkalmazás nem használ SSL az adatbázishoz való csatlakozáshoz, majd szeretné tiltani az SSL a MySQL-kiszolgálón. További részletek az SSL konfigurálása: [SSL használatával az Azure Database for MySQL](howto-configure-ssl.md).

## <a name="next-steps"></a>További lépések
Kapcsolati karakterláncokkel kapcsolatos további információkért tekintse meg [kapcsolati karakterláncok](howto-connection-string.md).
