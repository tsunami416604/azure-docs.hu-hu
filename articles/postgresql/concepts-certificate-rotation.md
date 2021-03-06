---
title: Tanúsítvány elforgatása Azure Database for PostgreSQL egyetlen kiszolgálóhoz
description: Ismerje meg a főtanúsítványok változásainak a Azure Database for PostgreSQL egyetlen kiszolgálót érintő közelgő változásait
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/02/2020
ms.openlocfilehash: 96720e156963a5fb542e72823a602aa2cc6a0ead
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2020
ms.locfileid: "93349001"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-database-for-postgresql-single-server"></a>A legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ változásának megértése Azure Database for PostgreSQL egyetlen kiszolgálón

Az [adatbázis-kiszolgálóhoz való kapcsolódáshoz](concepts-connectivity-architecture.md)a Azure Database for PostgreSQL módosítja az SSL protokollal engedélyezett ügyfélalkalmazás vagy illesztőprogram főtanúsítványát. A jelenleg elérhető főtanúsítvány a szokásos karbantartási és biztonsági eljárások részeként 2021 (02/15/2021). február 15-én lejár. Ez a cikk további részleteket tartalmaz a közelgő változásokról, az érintett erőforrásokról és azokról a lépésekről, amelyekkel biztosítható, hogy az alkalmazás fenntartsa az adatbázis-kiszolgálóval való kapcsolatot.

>[!NOTE]
> Az ügyfelek visszajelzései alapján a meglévő Baltimore legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ főtanúsítványának elavulttá tételét a 2020. október 15. és 2021. között meghosszabbítottuk. Reméljük, hogy ez a bővítmény elegendő időt biztosít ahhoz, hogy a felhasználók megváltoztassák az ügyfél módosításait, ha azok hatással vannak rájuk.

## <a name="what-update-is-going-to-happen"></a>Milyen frissítés fog történni?

Bizonyos esetekben az alkalmazások egy megbízható hitelesítésszolgáltató (CA) tanúsítványfájl alapján létrehozott helyi Tanúsítványfájl használatával csatlakoznak a biztonságos hálózathoz. Jelenleg az ügyfelek csak az előre definiált tanúsítványt használhatják az [itt](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)található Azure Database for PostgreSQL-kiszolgálóhoz való kapcsolódáshoz. Azonban a HITELESÍTÉSSZOLGÁLTATÓ [(CA) böngésző fóruma](https://cabforum.org/) nemrég közzétette a CA-gyártók által kiállított több tanúsítvány jelentéseit, amelyek nem megfelelőek.

Az iparág megfelelőségi követelményeinek megfelelően a CA-szállítók megkezdték a CA-tanúsítványok visszavonását a nem megfelelő hitelesítésszolgáltatók számára, és a kiszolgálókat a megfelelő hitelesítésszolgáltatók által kiadott tanúsítványok használatára kötelezik, valamint a megfelelő hitelesítésszolgáltatóktól származó HITELESÍTÉSSZOLGÁLTATÓI tanúsítványok aláírásával. Mivel a Azure Database for PostgreSQL jelenleg a nem megfelelő tanúsítványok egyikét használja, amelyeket az ügyfélalkalmazások az SSL-kapcsolataik ellenőrzéséhez használnak, biztosítaniuk kell, hogy a megfelelő lépéseket (lásd alább) a PostgreSQL-kiszolgálókra gyakorolt lehetséges hatás csökkentése érdekében.

Az új tanúsítvány az 2021-as (02/15/2021-as) 15. február 15-ig lesz használatban. Ha a kiszolgáló tanúsítványának HITELESÍTÉSSZOLGÁLTATÓI érvényesítését vagy teljes körű érvényesítését használja a PostgreSQL-ügyfélről való csatlakozáshoz (sslmode = ellenőrzés-CA vagy sslmode = validate-Full), akkor az alkalmazás konfigurációját a 2021. február 15. előtt kell frissítenie (02/15/2021).

## <a name="how-do-i-know-if-my-database-is-going-to-be-affected"></a>Hogyan tudni, hogy az adatbázis érintett lesz-e?

Minden SSL/TLS protokollt használó alkalmazás, és ellenőrizze, hogy a főtanúsítványnak frissítenie kell-e a főtanúsítványt. A kapcsolati karakterlánc áttekintésével megtekintheti, hogy a kapcsolatok ellenőrzik-e a főtanúsítványt.
-    Ha a kapcsolódási sztring tartalmaz `sslmode=verify-ca` vagy `sslmode=verify-full` , frissítenie kell a tanúsítványt.
-    Ha a kapcsolódási karakterlánc magában foglalja a, a, a `sslmode=disable` `sslmode=allow` vagy a `sslmode=prefer` `sslmode=require` , nem kell frissítenie a tanúsítványokat. 
-    Ha a kapcsolódási karakterlánc nem ad meg sslmode, nem szükséges frissítenie a tanúsítványokat.

Ha olyan ügyfelet használ, amely el tudja olvasni a kapcsolódási karakterláncot, tekintse át az ügyfél dokumentációját, és Ismerje meg, hogy igazolja-e a tanúsítványokat.

A PostgreSQL sslmode megismeréséhez tekintse át az [SSL-mód leírásait](https://www.postgresql.org/docs/11/libpq-ssl.html#ssl-mode-descriptions) a PostgreSQL dokumentációjában.

Annak elkerülése érdekében, hogy az alkalmazás rendelkezésre állása megszakadjon a tanúsítványok váratlan visszavonása vagy a visszavont tanúsítvány frissítése miatt, tekintse meg a [**"mire van szükség a kapcsolat fenntartásához"**](concepts-certificate-rotation.md#what-do-i-need-to-do-to-maintain-connectivity) című szakaszt.

## <a name="what-do-i-need-to-do-to-maintain-connectivity"></a>Mit kell tennem a kapcsolat fenntartásához

Az alábbi lépéseket követve elkerülhető, hogy az alkalmazás a tanúsítványok váratlanul visszavont állapotba helyezése miatt megszakadjon, vagy egy visszavont tanúsítvány frissítését. Az a gondolat, hogy létre kell hoznia egy új *. PEM* -fájlt, amely kombinálja az aktuális tanúsítványt és az újat, és az SSL-tanúsítvány érvényesítése során az engedélyezett értékek egyszer lesznek használva. Tekintse át az alábbi lépéseket:

*   Töltse le a BaltimoreCyberTrustRoot & DigiCertGlobalRootG2 legfelső szintű HITELESÍTÉSSZOLGÁLTATÓját az alábbi hivatkozások közül:
    *   https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem
    *   https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem

*   Kombinált HITELESÍTÉSSZOLGÁLTATÓI tanúsítványtároló létrehozása a **BaltimoreCyberTrustRoot** -és **DigiCertGlobalRootG2** -tanúsítványokkal együtt.
    *   A Java (PostgreSQL JDBC) felhasználók számára a DefaultJavaSSLFactory használatával futtassa a következőt:

          ```console
          keytool -importcert -alias PostgreSQLServerCACert  -file D:\BaltimoreCyberTrustRoot.crt.pem  -keystore truststore -storepass password -noprompt
          ```

          ```console
          keytool -importcert -alias PostgreSQLServerCACert2  -file D:\DigiCertGlobalRootG2.crt.pem -keystore truststore -storepass password  -noprompt
          ```

          Ezután cserélje le az eredeti alaptároló fájlját az új létrehozott-re:
        *   System. setProperty ("javax. net. SSL. trustStore", "path_to_truststore_file"); 
        *   System. setProperty ("javax. net. SSL. trustStorePassword", "password");
        
    *   Windows rendszerű .NET-(Npgsql-) felhasználók esetén győződjön meg arról, hogy a Windows-tanúsítványtárolóban, a megbízható legfelső szintű hitelesítésszolgáltatóknál a **Baltimore CyberTrust root** és a **DigiCert globális root G2** is létezik. Ha valamelyik tanúsítvány nem létezik, importálja a hiányzó tanúsítványt.

        ![.Net-tanúsítvány Azure Database for PostgreSQL](media/overview/netconnecter-cert.png)

    *   A .NET-(Npgsql-) felhasználók számára a SSL_CERT_DIR használatával Linux rendszeren a **BaltimoreCyberTrustRoot** és a **DigiCertGlobalRootG2** egyaránt létezik a SSL_CERT_DIR által jelzett könyvtárban. Ha valamelyik tanúsítvány nem létezik, hozza létre a hiányzó tanúsítványfájl-fájlt.

    *   A PostgreSQL-ügyfél többi felhasználója számára egyesítheti az alábbi formátumhoz hasonló két HITELESÍTÉSSZOLGÁLTATÓI tanúsítványfájl-fájlt

        </br>-----A TANÚSÍTVÁNY MEGKEZDÉSE-----
 </br>(Root CA1: BaltimoreCyberTrustRoot. CRT. PEM)
 </br>-----ZÁRÓ TANÚSÍTVÁNY-----
 </br>-----A TANÚSÍTVÁNY MEGKEZDÉSE-----
 </br>(Root CA2: DigiCertGlobalRootG2. CRT. PEM)
 </br>-----ZÁRÓ TANÚSÍTVÁNY-----

*   Cserélje le az eredeti legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI PEM-fájlt a kombinált legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI fájlra, és indítsa újra az alkalmazást/ügyfelet.
*    A jövőben a kiszolgálói oldalon üzembe helyezett új tanúsítvány után a HITELESÍTÉSSZOLGÁLTATÓ PEM-fájlját a DigiCertGlobalRootG2. CRT. PEM értékre módosíthatja.

## <a name="what-can-be-the-impact-of-not-updating-the-certificate"></a>Milyen hatással lehet a tanúsítvány frissítésére?
Ha a Baltimore CyberTrust legfelső szintű tanúsítványát használja az Azure Database for PostgreSQL az SSL-kapcsolat ellenőrzéséhez az itt dokumentált módon, előfordulhat, hogy az alkalmazás rendelkezésre állása megszakad, mert az adatbázis nem érhető el. Az alkalmazástól függően különböző hibaüzenetek jelenhetnek meg, többek között a következők:
*    Érvénytelen tanúsítvány/visszavont tanúsítvány
*    A kapcsolat időtúllépés miatt megszakadt

> [!NOTE]
> Ne dobja el vagy ne változtassa meg a **Baltimore-tanúsítványt** , amíg meg nem történik a tanúsítvány módosítása. A módosítások elvégzése után a rendszer elküld egy kommunikációt, amely után a Baltimore-tanúsítvány eldobása biztonságos. 

## <a name="frequently-asked-questions"></a>Gyakori kérdések

###    <a name="1-if-i-am-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>1. Ha nem használok SSL/TLS-t, továbbra is frissíteni kell a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓT?
Nincs szükség műveletre, ha nem SSL/TLS protokollt használ. 

### <a name="2-if-i-am-using-ssltls-do-i-need-to-restart-my-database-server-to-update-the-root-ca"></a>2. ha SSL/TLS protokollt használok, a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ frissítéséhez újra kell indítani az adatbázis-kiszolgálót?
Nem, nem kell újraindítani az adatbázis-kiszolgálót az új tanúsítvány használatának megkezdéséhez. Ez egy ügyféloldali változás, és a bejövő ügyfélkapcsolatoknak az új tanúsítvánnyal kell rendelkezniük ahhoz, hogy az adatbázis-kiszolgálóhoz csatlakozzanak.

### <a name="3-what-will-happen-if-i-do-not-update-the-root-certificate-before-february-15-2021-02152021"></a>3. mi történik, ha nem frissítem a főtanúsítványt 2021. február 15. előtt (02/15/2021)?
Ha nem frissíti a főtanúsítványt a 2021 (02/15/2021) február 15. előtt, az SSL/TLS protokollon keresztül csatlakozó alkalmazások és a főtanúsítvány ellenőrzése nem fog tudni kommunikálni a PostgreSQL-adatbázis-kiszolgálóval, és az alkalmazás kapcsolati problémákat tapasztal a PostgreSQL-adatbázis-kiszolgálóval kapcsolatban.

### <a name="4-what-is-the-impact-if-using-app-service-with-azure-database-for-postgresql"></a>4. milyen hatással van a App Service és a Azure Database for PostgreSQL használata?
Az Azure app Services esetében a Azure Database for PostgreSQLhoz való csatlakozás két lehetséges forgatókönyvet tartalmazhat, amelyek attól függnek, hogy miként használja az SSL-t az alkalmazással.
*   Ez az új tanúsítvány App Service platform szinten lett hozzáadva. Ha az alkalmazásban App Service platformon található SSL-tanúsítványokat használja, nincs szükség beavatkozásra.
*   Ha explicit módon az SSL-tanúsítvány elérési útját is tartalmazza a kódban, akkor le kell töltenie az új tanúsítványt, és frissítenie kell a kódot az új tanúsítvány használatára. Jó példa erre a forgatókönyvre, ha egyéni tárolókat használ a App Service a [app Service dokumentációjában](../app-service/tutorial-multi-container-app.md#configure-database-variables-in-wordpress) megosztva.

### <a name="5-what-is-the-impact-if-using-azure-kubernetes-services-aks-with-azure-database-for-postgresql"></a>5. milyen hatással van az Azure Kubernetes Services (ak) használata a Azure Database for PostgreSQL?
Ha az Azure Kubernetes Services (ak) használatával próbál csatlakozni a Azure Database for PostgreSQLhoz, hasonló a dedikált ügyfelek gazdagép-környezetéhez való hozzáféréshez. Tekintse át a lépéseket [itt](../aks/ingress-own-tls.md).

### <a name="6-what-is-the-impact-if-using-azure-data-factory-to-connect-to-azure-database-for-postgresql"></a>6. milyen hatással van a Azure Data Factory használata a Azure Database for PostgreSQLhoz való kapcsolódásra?
A Azure Integration Runtimet használó összekötők esetében az összekötő kihasználja a Windows tanúsítványtárolóban található tanúsítványokat az Azure által üzemeltetett környezetben. Ezek a tanúsítványok már kompatibilisek az újonnan alkalmazott tanúsítványokkal, ezért nincs szükség beavatkozásra.

A saját üzemeltetésű Integration Runtime használó összekötő esetében, ha explicit módon felveszi az SSL-tanúsítvány elérési útját a kapcsolati karakterláncba, le kell töltenie az [új tanúsítványt](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) , és frissítenie kell a kapcsolati karakterláncot a használatára.

### <a name="7-do-i-need-to-plan-a-database-server-maintenance-downtime-for-this-change"></a>7. meg kell tervezni egy adatbázis-kiszolgáló karbantartási állásidőt ehhez a változáshoz?
Nem. Mivel a változás csak az ügyféloldali oldalon csatlakozik az adatbázis-kiszolgálóhoz, nincs szükség karbantartási állásidőre az adatbázis-kiszolgáló számára ehhez a változáshoz.

### <a name="8--what-if-i-cannot-get-a-scheduled-downtime-for-this-change-before-february-15-2021-02152021"></a>8. mi történik, ha nem tudok ütemezett állásidőt beolvasni ehhez a változáshoz a 2021. február 15. előtt (02/15/2021)?
Mivel a kiszolgálóhoz való csatlakozáshoz használt ügyfeleknek frissíteniük kell a tanúsítvány adatait a [javítás szakaszban leírtak szerint,](./concepts-certificate-rotation.md#what-do-i-need-to-do-to-maintain-connectivity)ebben az esetben nem kell leállást biztosítani a kiszolgálónak.

### <a name="9-if-i-create-a-new-server-after-february-15-2021-02152021-will-i-be-impacted"></a>9. Ha új kiszolgálót hoz létre a 2021 (02/15/2021) február 15. után, hatással leszek rá?
Az 02/15/2021 2021-es február 15. után létrehozott kiszolgálók esetében az SSL használatával történő kapcsolódáshoz használhatja az újonnan kiállított tanúsítványt az alkalmazásaihoz.

###    <a name="10-how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>10. milyen gyakran frissíti a Microsoft a tanúsítványait, vagy mi a lejárati szabályzat?
Az Azure Database for PostgreSQL által használt tanúsítványokat a megbízható hitelesítésszolgáltatók (CA) biztosítják. Így a tanúsítványok Azure Database for PostgreSQL-on való támogatása a CA által támogatott tanúsítványok támogatásához van kötve. Ebben az esetben azonban előfordulhat, hogy az előre meghatározott tanúsítványokban nem előre látható hibák vannak, amelyeket a lehető leghamarabb meg kell oldani.

###    <a name="11-if-i-am-using-read-replicas-do-i-need-to-perform-this-update-only-on-the-primary-server-or-the-read-replicas"></a>11. ha olvasási replikákat használok, ezt a frissítést csak az elsődleges kiszolgálón vagy az olvasási replikán kell elvégezni?
Mivel ez a frissítés ügyféloldali módosítás, ha az ügyfél a másodpéldány-kiszolgálóról olvassa az adatok olvasását, akkor a módosításokat is alkalmaznia kell az ügyfeleken. 

### <a name="12-do-we-have-server-side-query-to-verify-if-ssl-is-being-used"></a>12. van-e kiszolgálóoldali lekérdezés annak ellenőrzéséhez, hogy az SSL használatban van-e?
Annak ellenőrzéséhez, hogy SSL-kapcsolatot használ-e a kiszolgálóhoz való kapcsolódáshoz, tekintse meg az [SSL-ellenőrzést](concepts-ssl-connection-security.md#applications-that-require-certificate-verification-for-tls-connectivity).

### <a name="13-is-there-an-action-needed-if-i-already-have-the-digicertglobalrootg2-in-my-certificate-file"></a>13. van szükség beavatkozásra, ha már van a DigiCertGlobalRootG2 a saját tanúsítványfájl?
Nem. Nincs szükség beavatkozásra, ha a tanúsítványfájl már rendelkezik a **DigiCertGlobalRootG2**.

### <a name="14-what-is-you-are-using-docker-image-of-pgbouncer-sidecar-provided-by-microsoft"></a>14. mit használ a Microsoft által biztosított PgBouncer oldalkocsi Docker-rendszerképe?
[Itt](https://hub.docker.com/_/microsoft-azure-oss-db-tools-pgbouncer-sidecar) jelennek meg az alábbi, a [**Baltimore**](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) -t és a [**DigiCert**](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) támogató új Docker-rendszerkép (legújabb címke). Az új rendszerkép lekérésével elkerülheti, hogy a kapcsolat megszakítása 2021. február 15-én kezdődik. 

###    <a name="15-what-if-i-have-further-questions"></a>15. Mi a teendő, ha további kérdéseim vannak?
Ha kérdése van, választ kaphat a [Microsoft Q&a](mailto:AzureDatabaseforPostgreSQL@service.microsoft.com)közösségi szakértőitől. Ha támogatási csomaggal rendelkezik, és technikai segítségre van szüksége,  [vegye fel velünk a kapcsolatot](mailto:AzureDatabaseforPostgreSQL@service.microsoft.com)
