---
title: Tanúsítvány elforgatása Azure Database for MySQL
description: További információ a főtanúsítványok változásairól, amelyek hatással lesznek a Azure Database for MySQL
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 09/02/2020
ms.openlocfilehash: 7e78c0d2538dbb6326df30d63ec1c38de48b62e6
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2020
ms.locfileid: "90057187"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-database-for-mysql"></a>A legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ változásának megismerése Azure Database for MySQL

Az [adatbázis-kiszolgálóhoz való kapcsolódáshoz](concepts-connectivity-architecture.md)a Azure Database for MySQL módosítja az SSL protokollal engedélyezett ügyfélalkalmazás vagy illesztőprogram főtanúsítványát. A jelenleg elérhető főtanúsítvány a szokásos karbantartási és biztonsági eljárások részeként 2020 (10/26/2020) értékű. Ez a cikk további részleteket tartalmaz a közelgő változásokról, az érintett erőforrásokról és azokról a lépésekről, amelyekkel biztosítható, hogy az alkalmazás fenntartsa az adatbázis-kiszolgálóval való kapcsolatot.

## <a name="what-update-is-going-to-happen"></a>Milyen frissítés fog történni?

Bizonyos esetekben az alkalmazások egy megbízható hitelesítésszolgáltató (CA) tanúsítványfájl alapján létrehozott helyi Tanúsítványfájl használatával csatlakoznak a biztonságos hálózathoz. Jelenleg az ügyfelek csak az előre definiált tanúsítványt használhatják az [itt](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)található Azure Database for MySQL-kiszolgálóhoz való kapcsolódáshoz. Azonban a hitelesítésszolgáltató [(CA) böngésző fóruma](https://cabforum.org/)   nemrég közzétette a CA-gyártók által kiállított több tanúsítvány jelentéseit, amelyek nem megfelelőek.

Az iparág megfelelőségi követelményeinek megfelelően a CA-szállítók megkezdték a CA-tanúsítványok visszavonását a nem megfelelő hitelesítésszolgáltatók számára, és a kiszolgálókat a megfelelő hitelesítésszolgáltatók által kiadott tanúsítványok használatára kötelezik, valamint a megfelelő hitelesítésszolgáltatóktól származó HITELESÍTÉSSZOLGÁLTATÓI tanúsítványok aláírásával. Mivel a Azure Database for MySQL jelenleg ezen nem megfelelő tanúsítványok egyikét használja, amelyeket az ügyfélalkalmazások az SSL-kapcsolataik ellenőrzéséhez használnak, biztosítaniuk kell, hogy a megfelelő lépéseket (lásd alább) a MySQL-kiszolgálókra gyakorolt lehetséges hatás csökkentése érdekében.

Az új tanúsítvány a 2020-es (10/26/2020-as) október 26-án kezdődően lesz használatban. Ha a kiszolgáló tanúsítványának CA-érvényesítését vagy teljes körű érvényesítését használja egy MySQL-ügyfélről való kapcsolódáskor (sslmode = ellenőrzés-CA vagy sslmode = validate-Full), az alkalmazás konfigurációját a 2020 (10/26/2020) előtt kell frissítenie.

## <a name="how-do-i-know-if-my-database-is-going-to-be-affected"></a>Hogyan tudni, hogy az adatbázis érintett lesz-e?

Az SSL/TLS protokollt használó összes alkalmazás és annak ellenőrzése, hogy a főtanúsítványnak frissítenie kell-e a főtanúsítványt a Azure Database for MySQLhoz való kapcsolódáshoz. Ha jelenleg nem használ SSL/TLS-t, az alkalmazás rendelkezésre állása nincs hatással. [Itt](concepts-ssl-connection-security.md#ssl-default-settings)ellenőrizheti, hogy az ügyfélalkalmazás megpróbál-e SSL-módot használni az előre definiált megbízható HITELESÍTÉSSZOLGÁLTATÓ (CA) használatával.

Annak elkerülése érdekében, hogy az alkalmazás rendelkezésre állása megszakadjon a tanúsítványok váratlan visszavonása vagy a visszavont tanúsítvány frissítése miatt, tekintse meg a [**"mire van szükség a kapcsolat fenntartásához"**](concepts-certificate-rotation.md#what-do-i-need-to-do-to-maintain-connectivity) című szakaszt.

## <a name="what-do-i-need-to-do-to-maintain-connectivity"></a>Mit kell tennem a kapcsolat fenntartásához

Az alábbi lépések végrehajtásával elkerülhető, hogy az alkalmazás rendelkezésre állása megszakadjon a tanúsítványok váratlan visszavonása miatt, vagy egy visszavont tanúsítvány frissítése:

*   Töltse le a BaltimoreCyberTrustRoot & DigiCertGlobalRootG2 legfelső szintű HITELESÍTÉSSZOLGÁLTATÓját az alábbi hivatkozások közül:
    *   https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem
    *   https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem

*   Kombinált HITELESÍTÉSSZOLGÁLTATÓI tanúsítványtároló létrehozása a **BaltimoreCyberTrustRoot** -és **DigiCertGlobalRootG2** -tanúsítványokkal együtt.
    *   A Java (MySQL Connector/J) felhasználók számára futtassa a következőt:

          ```azurecli-interactive
          keytool -importcert -alias MySQLServerCACert  -file D:\BaltimoreCyberTrustRoot.crt.pem  -keystore truststore -storepass password -noprompt
          ```

          ```azurecli-interactive
          keytool -importcert -alias MySQLServerCACert2  -file D:\DigiCertGlobalRootG2.crt.pem -keystore truststore -storepass password  -noprompt
          ```

          Ezután cserélje le az eredeti alaptároló fájlját az új létrehozott-re:
        *   System. setProperty ("javax. net. SSL. trustStore", "path_to_truststore_file"); 
        *   System. setProperty ("javax. net. SSL. trustStorePassword", "password");

    *   .NET (MySQL Connector/NET, MySQLConnector) felhasználók esetén győződjön meg arról, hogy a **BaltimoreCyberTrustRoot** és a **DigiCertGlobalRootG2** egyaránt létezik a Windows tanúsítványtárolóban, a megbízható legfelső szintű hitelesítésszolgáltatókban. Ha valamelyik tanúsítvány nem létezik, importálja a hiányzó tanúsítványt.

        ![.Net-tanúsítvány Azure Database for MySQL](media/overview/netconnecter-cert.png)

    *   A Linux rendszerű .NET-felhasználók SSL_CERT_DIRon való használata esetén győződjön meg arról, hogy a **BaltimoreCyberTrustRoot** és a **DigiCertGlobalRootG2** egyaránt létezik a SSL_CERT_DIR által jelzett könyvtárban. Ha valamelyik tanúsítvány nem létezik, hozza létre a hiányzó tanúsítványfájl-fájlt.

    *   Más (MySQL Client/MySQL Workbench/C/C++/Go/Python/Ruby/PHP/NodeJS/Perl/Swift) felhasználók esetén egyesítheti két HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány fájlját, például az alábbi formátumot:</b>

        </br>-----A TANÚSÍTVÁNY MEGKEZDÉSE-----
 </br>(Root CA1: BaltimoreCyberTrustRoot. CRT. PEM)
 </br>-----ZÁRÓ TANÚSÍTVÁNY-----
 </br>-----A TANÚSÍTVÁNY MEGKEZDÉSE-----
 </br>(Root CA2: DigiCertGlobalRootG2. CRT. PEM)
 </br>-----ZÁRÓ TANÚSÍTVÁNY-----

*   Cserélje le az eredeti legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI PEM-fájlt a kombinált legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI fájlra, és indítsa újra az alkalmazást/ügyfelet.
*   A jövőben a kiszolgálói oldalon üzembe helyezett új tanúsítvány után a HITELESÍTÉSSZOLGÁLTATÓ PEM-fájlját a DigiCertGlobalRootG2. CRT. PEM értékre módosíthatja.

## <a name="what-can-be-the-impact"></a>Milyen hatással lehet?
Ha az itt dokumentált Azure Database for MySQL kiállított tanúsítványt használja, előfordulhat, hogy az alkalmazás rendelkezésre állása megszakad, mert az adatbázis nem érhető el. Az alkalmazástól függően különböző hibaüzenetek jelenhetnek meg, többek között a következők:
*   Érvénytelen tanúsítvány/visszavont tanúsítvány
*   A kapcsolat időtúllépés miatt megszakadt
*   Hiba, ha van ilyen

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="1-if-i-am-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>1. Ha nem használok SSL/TLS-t, továbbra is frissíteni kell a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓT?
Nincs szükség műveletre, ha nem SSL/TLS protokollt használ. 

### <a name="2-if-i-am-using-ssltls-do-i-need-to-restart-my-database-server-to-update-the-root-ca"></a>2. ha SSL/TLS protokollt használok, a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ frissítéséhez újra kell indítani az adatbázis-kiszolgálót?
Nem, nem kell újraindítani az adatbázis-kiszolgálót az új tanúsítvány használatának megkezdéséhez. Ez a főtanúsítvány egy ügyféloldali változás, és a bejövő ügyfélkapcsolatoknak az új tanúsítvánnyal kell rendelkezniük ahhoz, hogy az adatbázis-kiszolgálóhoz csatlakozzanak.

### <a name="3-what-will-happen-if-i-do-not-update-the-root-certificate-before-october-26-2020-10262020"></a>3. mi történik, ha nem frissítem a főtanúsítványt a 2020-es (10/26/2020-as) október 26. előtt?
Ha a főtanúsítványt a 2020. november 30. előtt nem frissíti, az SSL/TLS protokollon keresztül csatlakozó alkalmazások és a főtanúsítvány ellenőrzése nem fog tudni kommunikálni a MySQL adatbázis-kiszolgálóval, és az alkalmazás kapcsolódási problémákat tapasztal a MySQL-adatbázis-kiszolgálóval kapcsolatban.

### <a name="4-do-i-need-to-plan-a-maintenance-downtime-for-this-changebr"></a>4. meg kell tervezni egy karbantartási állásidőt ehhez a változáshoz?<BR>
Nem. Mivel a változás csak az ügyféloldali oldalon történik az adatbázis-kiszolgálóhoz való kapcsolódáshoz, ehhez a változáshoz nem szükséges karbantartási állásidő.

### <a name="5--what-if-i-cannot-get-a-scheduled-downtime-for-this-change-before-october-26-2020-10262020"></a>5. Mi a teendő, ha nem tudok ütemezett állásidőt beolvasni ehhez a változáshoz, 2020 (10/26/2020). október 26-án?
Mivel a kiszolgálóhoz való csatlakozáshoz használt ügyfeleknek frissíteniük kell a tanúsítvány adatait a [javítás szakaszban leírtak szerint,](./concepts-certificate-rotation.md#what-do-i-need-to-do-to-maintain-connectivity)ebben az esetben nem kell leállást biztosítani a kiszolgálónak.

###  <a name="6-if-i-create-a-new-server-after-nov-30-2020-will-i-be-impacted"></a>6. Ha egy új kiszolgálót 2020 november 30. után hozunk létre, hatással leszek rá?
Az 2020 (10/26/2020) után létrehozott kiszolgálók esetében az alkalmazásokhoz tartozó újonnan kiadott tanúsítványt használhatja az SSL használatával történő kapcsolódáshoz.

### <a name="7-how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>7. milyen gyakran frissíti a Microsoft a tanúsítványait, vagy mi a lejárati szabályzat?
Az Azure Database for MySQL által használt tanúsítványokat a megbízható hitelesítésszolgáltatók (CA) biztosítják. Így a tanúsítványok Azure Database for MySQL-on való támogatása a CA által támogatott tanúsítványok támogatásához van kötve. Ebben az esetben azonban előfordulhat, hogy az előre meghatározott tanúsítványokban nem előre látható hibák vannak, amelyeket a lehető leghamarabb meg kell oldani.

### <a name="8-if-i-am-using-read-replicas-do-i-need-to-perform-this-update-only-on-master-server-or-all-the-read-replicas"></a>8. ha olvasási replikákat használok, ezt a frissítést csak a főkiszolgálón vagy az összes olvasási replikán kell elvégezni?
Mivel ez a frissítés ügyféloldali módosítás, ha az ügyfél a másodpéldány-kiszolgálóról olvassa az adatok olvasását, akkor a módosításokat is alkalmazni kell az ügyfeleken. 

### <a name="9-do-we-have-server-side-query-to-verify-if-ssl-is-being-used"></a>9. van-e kiszolgálóoldali lekérdezés annak ellenőrzéséhez, hogy az SSL használatban van-e?
Annak ellenőrzéséhez, hogy SSL-kapcsolatot használ-e a kiszolgálóhoz való kapcsolódáshoz, tekintse meg az [SSL-ellenőrzést](howto-configure-ssl.md#step-4-verify-the-ssl-connection).

### <a name="10-what-if-i-have-further-questions"></a>10. Mi a teendő, ha további kérdéseim vannak?
Ha kérdése van, választ kaphat a [Microsoft Q&a](mailto:AzureDatabaseforMySQL@service.microsoft.com)közösségi szakértőitől. Ha támogatási csomaggal rendelkezik, és technikai segítségre van szüksége,  [vegye fel velünk a kapcsolatot](mailto:AzureDatabaseforMySQL@service.microsoft.com)