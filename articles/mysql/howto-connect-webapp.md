---
title: Csatlakozás az Azure App Service szolgáltatáshoz – Azure-adatbázis a MySQL-hez
description: Útmutató egy meglévő Azure App Service megfelelő csatlakoztatásához a MySQL-alapú Azure Database-hez
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: ba75daa47ca1f77cd5828d13877238a64deeeb41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062460"
---
# <a name="connect-an-existing-azure-app-service-to-azure-database-for-mysql-server"></a>Meglévő Azure App Service csatlakoztatása a MySQL-kiszolgálóhoz készült Azure Database-hez
Ez a témakör bemutatja, hogyan csatlakoztathat egy meglévő Azure App Service-t az Azure Database for MySQL-kiszolgálóhoz.

## <a name="before-you-begin"></a>Előkészületek
Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Hozzon létre egy Azure-adatbázist a MySQL-kiszolgálóhoz. További információt a [MySQL-kiszolgáló Azure-adatbázisának létrehozása](quickstart-create-mysql-server-database-using-azure-portal.md) a Portálról vagy az Azure Database for [MySQL-kiszolgáló létrehozása a CLI használatával című részében](quickstart-create-mysql-server-database-using-azure-cli.md)talál.

Jelenleg két megoldás van, amelyek lehetővé teszik a hozzáférést egy Azure App Service-ből egy Azure-adatbázis a MySQL.Currently are two solutions to enable access from an Azure App Service to an Azure Database for MySQL. Mindkét megoldás kiszolgálószintű tűzfalszabályok beállítását foglalja magában.

## <a name="solution-1---allow-azure-services"></a>1. megoldás – Az Azure-szolgáltatások engedélyezése
Az Azure Database for MySQL tűzfallal biztosítja a hozzáférési biztonságot az adatok védelme érdekében. Amikor egy Azure App Service-ről csatlakozik az Azure Database for MySQL-kiszolgálóhoz, ne feledje, hogy az App Service kimenő IP-címét dinamikus jellegűek. Az "Azure-szolgáltatásokhoz való hozzáférés engedélyezése" beállítás kiválasztásával az alkalmazásszolgáltatás csatlakozhat a MySQL-kiszolgálóhoz.

1. A MySQL-kiszolgáló panelen a Beállítások cím alatt kattintson a **Kapcsolatbiztonság** elemre a Kapcsolatbiztonság panel mySQL-hez való megnyitásához.

   ![Azure Portal – kattintson a Kapcsolatbiztonság elemre](./media/howto-connect-webapp/1-connection-security.png)

2. Válassza **a BE** lehetőséget az **Azure-szolgáltatásokhoz való hozzáférés engedélyezése**, majd a Mentés **lehetőséget.**
   ![Azure Portal – Az Azure-hozzáférés engedélyezése](./media/howto-connect-webapp/allow-azure.png)

## <a name="solution-2---create-a-firewall-rule-to-explicitly-allow-outbound-ips"></a>2. megoldás – Tűzfalszabály létrehozása a kimenő IP-k kifejezett engedélyezéséhez
Explicit módon hozzáadhatja az Azure App Service összes kimenő IP-kiszolgálóját.

1. Az App Service tulajdonságai panelen tekintse meg a **kimenő IP-címet.**

   ![Azure Portal – Kimenő IP-k megtekintése](./media/howto-connect-webapp/2_1-outbound-ip-address.png)

2. A MySQL-kapcsolat biztonsági panelen egyenként adja hozzá a kimenő IP-eket.

   ![Azure Portal – Explicit IP-k hozzáadása](./media/howto-connect-webapp/2_2-add-explicit-ips.png)

3. Ne **felejtse** el menteni a tűzfalszabályokat.

Bár az Azure App-szolgáltatás megpróbálja az IP-címek állandó idővel, vannak olyan esetek, ahol az IP-címek változhatnak. Ez például akkor fordulhat elő, ha az alkalmazás újrahasznosítja, vagy egy méretezési művelet történik, vagy ha új számítógépeket adnak hozzá az Azure regionális adatközpontjaiban a kapacitás növelése érdekében. Amikor az IP-címek megváltoznak, az alkalmazás leállást tapasztalhat abban az esetben, ha már nem tud csatlakozni a MySQL kiszolgálóhoz. Tartsa szem előtt ezt a szempontot az előző megoldások egyikének kiválasztásakor.

## <a name="ssl-configuration"></a>SSL-konfiguráció
A MySQL Azure Database ssl alapértelmezés szerint engedélyezve van. Ha az alkalmazás nem ssl-t használ az adatbázishoz való csatlakozáshoz, akkor le kell tiltania az SSL-t a MySQL-kiszolgálón. Az SSL konfigurálásáról az [SSL használata az Azure Database for MySQL című](howto-configure-ssl.md)témakörben talál további részleteket.

### <a name="django-pymysql"></a>Django (PyMySQL)
```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'quickstartdb',
        'USER': 'myadmin@mydemoserver',
        'PASSWORD': 'yourpassword',
        'HOST': 'mydemoserver.mysql.database.azure.com',
        'PORT': '3306',
        'OPTIONS': {
            'ssl': {'ssl-ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'}
        }
    }
}
```

## <a name="next-steps"></a>További lépések
A kapcsolati karakterláncokról a [Kapcsolati karakterláncokról](howto-connection-string.md)további információt a Kapcsolati karakterláncok című dokumentum talál.
