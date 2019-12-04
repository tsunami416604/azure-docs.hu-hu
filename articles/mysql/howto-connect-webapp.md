---
title: Kapcsolódás Azure App Servicehoz – Azure Database for MySQL
description: A meglévő Azure App Service megfelelő összekapcsolásának lépései Azure Database for MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 63ef9ac55fcfaebfd58ae1ccdb34107d41900be5
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770543"
---
# <a name="connect-an-existing-azure-app-service-to-azure-database-for-mysql-server"></a>Meglévő Azure App Service összekötése Azure Database for MySQL-kiszolgálóval
Ez a témakör azt ismerteti, hogyan csatlakoztatható egy meglévő Azure App Service a Azure Database for MySQL-kiszolgálóhoz.

## <a name="before-you-begin"></a>Előzetes teendők
Jelentkezzen be az [Azure portálra](https://portal.azure.com). Hozzon létre egy Azure Database for MySQL-kiszolgálót. Részletekért lásd: [Azure Database for MySQL kiszolgáló létrehozása a portálról](quickstart-create-mysql-server-database-using-azure-portal.md) , vagy [Azure Database for MySQL kiszolgáló létrehozása a parancssori felület használatával](quickstart-create-mysql-server-database-using-azure-cli.md).

Jelenleg két megoldás áll rendelkezésre a Azure App Service és az Azure Database for MySQL közötti hozzáférés engedélyezéséhez. Mindkét megoldás a kiszolgálói szintű tűzfalszabályok beállítását is magában foglalja.

## <a name="solution-1---allow-azure-services"></a>1\. megoldás – Azure-szolgáltatások engedélyezése
A Azure Database for MySQL tűzfal használatával biztosít hozzáférést a biztonsághoz az adatai védelméhez. Ha Azure App Serviceról Azure Database for MySQL kiszolgálóra csatlakozik, vegye figyelembe, hogy a App Service kimenő IP-címeinek dinamikus jellegűek. Az "Azure-szolgáltatásokhoz való hozzáférés engedélyezése" beállítás kiválasztásával engedélyezheti az App Service számára a MySQL-kiszolgálóhoz való kapcsolódást.

1. A MySQL-kiszolgáló panelen, a beállítások fejléc alatt kattintson a **kapcsolatbiztonsági** elemre a Azure Database for MySQL kapcsolatbiztonsági paneljének megnyitásához.

   ![Azure Portal – kattintson a kapcsolatbiztonsági lehetőségre](./media/howto-connect-webapp/1-connection-security.png)

2. Válassza **a** be **lehetőséget az Azure-szolgáltatásokhoz való hozzáférés engedélyezése**, majd a **Mentés**lehetőségre.
   ![Azure Portal – Azure-hozzáférés engedélyezése](./media/howto-connect-webapp/allow-azure.png)

## <a name="solution-2---create-a-firewall-rule-to-explicitly-allow-outbound-ips"></a>2\. megoldás – tűzfalszabály létrehozása a kimenő IP-címek explicit engedélyezéséhez
Explicit módon hozzáadhatja a Azure App Service összes kimenő IP-címeit.

1. A App Service Tulajdonságok panelen tekintse meg a **kimenő IP-címet**.

   ![Azure Portal – kimenő IP-címek megtekintése](./media/howto-connect-webapp/2_1-outbound-ip-address.png)

2. A MySQL-kapcsolatok biztonsági paneljén egyenként adja hozzá a kimenő IP-címeket.

   ![Azure Portal – explicit IP-címek hozzáadása](./media/howto-connect-webapp/2_2-add-explicit-ips.png)

3. Ne felejtse el menteni a tűzfalszabályok **mentését** .

Bár az Azure app Service idővel megkísérli az IP-címek állandó megőrzését, az IP-címek változhatnak. Ez például akkor fordulhat elő, ha az alkalmazás újrahasznosítja vagy egy méretezési műveletet végez, vagy ha új számítógépeket adnak hozzá az Azure regionális adatközpontokban a kapacitás növeléséhez. Ha az IP-címek változnak, az alkalmazás leállást tapasztalhat abban az esetben, ha már nem tud csatlakozni a MySQL-kiszolgálóhoz. Vegye figyelembe, hogy az előző megoldások egyikének kiválasztásakor ne feledje.

## <a name="ssl-configuration"></a>SSL-konfiguráció
A Azure Database for MySQL alapértelmezés szerint engedélyezve van az SSL-ben. Ha az alkalmazás nem használ SSL-t az adatbázishoz való kapcsolódáshoz, le kell tiltania az SSL-t a MySQL-kiszolgálón. Az SSL konfigurálásával kapcsolatos további információkért lásd: [az SSL használata a Azure Database for MySQL használatával](howto-configure-ssl.md).

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

## <a name="next-steps"></a>Következő lépések
A kapcsolódási karakterláncokkal kapcsolatos további információkért tekintse meg a [kapcsolódási karakterláncokat](howto-connection-string.md).
