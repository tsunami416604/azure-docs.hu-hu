---
title: Hitelesítési problémák az Azure HDInsightban
description: Hitelesítési problémák az Azure HDInsightban
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 11/08/2019
ms.openlocfilehash: 26eec9cdd327ceb51e72deb1d6f40d585ce368fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75896136"
---
# <a name="authentication-issues-in-azure-hdinsight"></a>Hitelesítési problémák az Azure HDInsightban

Ez a cikk az Azure HDInsight-fürtökkel való kommunikáció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

Az Azure Data Lake (Gen1 vagy Gen2) által támogatott biztonságos fürtökön, amikor a tartományi felhasználók HDI Gateway-en keresztül jelentkeznek be a fürtszolgáltatásokba (például az Apache Ambari portálra való bejelentkezéskor), a HDI Gateway először megpróbál oauth-jogkivonatot beszerezni az Azure Active Directoryból (Azure AD) , majd kap egy Kerberos-jegyet az Azure AD DS-ből. A hitelesítés a következő szakaszok bármelyikében sikertelen lehet. Ez a cikk célja, hogy hibakeresést néhány ilyen kérdések.

Ha a hitelesítés sikertelen, a rendszer kéri a hitelesítő adatokat. Ha megszakítja ezt a párbeszédpanelt, a hibaüzenet kinyomtatásra kerül. Íme néhány gyakori hibaüzenet:

## <a name="invalid_grant-or-unauthorized_client-50126"></a>invalid_grant vagy unauthorized_client, 50126

### <a name="issue"></a>Probléma

Az 50126-os hibakóddal rendelkező összevont felhasználók bejelentkezése sikertelen (a bejelentkezés sikeres a felhőfelhasználók számára). A hibaüzenet a következőhöz hasonló:

```
Reason: Bad Request, Detailed Response: {"error":"invalid_grant","error_description":"AADSTS70002: Error validating credentials. AADSTS50126: Invalid username or password\r\nTrace ID: 09cc9b95-4354-46b7-91f1-efd92665ae00\r\n Correlation ID: 4209bedf-f195-4486-b486-95a15b70fbe4\r\nTimestamp: 2019-01-28 17:49:58Z","error_codes":[70002,50126], "timestamp":"2019-01-28 17:49:58Z","trace_id":"09cc9b95-4354-46b7-91f1-efd92665ae00","correlation_id":"4209bedf-f195-4486-b486-95a15b70fbe4"}
```

### <a name="cause"></a>Ok

Az 50126-os Azure `AllowCloudPasswordValidation` AD-hibakód azt jelenti, hogy a házirendet nem állította be a bérlő.

### <a name="resolution"></a>Megoldás:

Az Azure AD-bérlő vállalati rendszergazdájának engedélyeznie kell az Azure AD számára az ADFS-alapú biztonsági felhasználók jelszókikereséseit.  Alkalmazza `AllowCloudPasswordValidationPolicy` a [HDInsight Vállalati biztonsági csomag használata](../domain-joined/apache-domain-joined-architecture.md)című cikkben látható módon.

---

## <a name="invalid_grant-or-unauthorized_client-50034"></a>invalid_grant vagy unauthorized_client, 50034

### <a name="issue"></a>Probléma

A bejelentkezés sikertelen az 50034-es hibakóddal. A hibaüzenet a következőhöz hasonló:

```
{"error":"invalid_grant","error_description":"AADSTS50034: The user account Microsoft.AzureAD.Telemetry.Diagnostics.PII does not exist in the 0c349e3f-1ac3-4610-8599-9db831cbaf62 directory. To sign into this application, the account must be added to the directory.\r\nTrace ID: bbb819b2-4c6f-4745-854d-0b72006d6800\r\nCorrelation ID: b009c737-ee52-43b2-83fd-706061a72b41\r\nTimestamp: 2019-04-29 15:52:16Z", "error_codes":[50034],"timestamp":"2019-04-29 15:52:16Z","trace_id":"bbb819b2-4c6f-4745-854d-0b72006d6800", "correlation_id":"b009c737-ee52-43b2-83fd-706061a72b41"}
```

### <a name="cause"></a>Ok

A felhasználónév helytelen (nem létezik). A felhasználó nem ugyanazt a felhasználónevet használja, amely et az Azure Portalon használja.

### <a name="resolution"></a>Megoldás:

Használja ugyanazt a felhasználónevet, amely a portálon működik.

---

## <a name="invalid_grant-or-unauthorized_client-50053"></a>invalid_grant vagy unauthorized_client, 50053

### <a name="issue"></a>Probléma

A felhasználói fiók zárolva van, hibakód: 50053. A hibaüzenet a következőhöz hasonló:

```
{"error":"unauthorized_client","error_description":"AADSTS50053: You've tried to sign in too many times with an incorrect user ID or password.\r\nTrace ID: 844ac5d8-8160-4dee-90ce-6d8c9443d400\r\nCorrelation ID: 23fe8867-0e8f-4e56-8764-0cdc7c61c325\r\nTimestamp: 2019-06-06 09:47:23Z","error_codes":[50053],"timestamp":"2019-06-06 09:47:23Z","trace_id":"844ac5d8-8160-4dee-90ce-6d8c9443d400","correlation_id":"23fe8867-0e8f-4e56-8764-0cdc7c61c325"}
```

### <a name="cause"></a>Ok

Túl sok bejelentkezési kísérlet helytelen jelszóval.

### <a name="resolution"></a>Megoldás:

Várjon 30 percet, állítsa le a hitelesítést megkísérelt alkalmazásokat.

---

## <a name="invalid_grant-or-unauthorized_client-50053"></a>invalid_grant vagy unauthorized_client, 50053

### <a name="issue"></a>Probléma

A jelszó lejárt, hibakód: 50053. A hibaüzenet a következőhöz hasonló:

```
{"error":"user_password_expired","error_description":"AADSTS50055: Password is expired.\r\nTrace ID: 241a7a47-e59f-42d8-9263-fbb7c1d51e00\r\nCorrelation ID: c7fe4a42-67e4-4acd-9fb6-f4fb6db76d6a\r\nTimestamp: 2019-06-06 17:29:37Z","error_codes":[50055],"timestamp":"2019-06-06 17:29:37Z","trace_id":"241a7a47-e59f-42d8-9263-fbb7c1d51e00","correlation_id":"c7fe4a42-67e4-4acd-9fb6-f4fb6db76d6a","suberror":"user_password_expired","password_change_url":"https://portal.microsoftonline.com/ChangePassword.aspx"}
```

### <a name="cause"></a>Ok

A jelszó lejárt.

### <a name="resolution"></a>Megoldás:

Módosítsa a jelszót az Azure Portalon (a helyszíni rendszerben), majd várjon 30 percet, amíg a szinkronizálás felzárkózik.

---

## <a name="interaction_required"></a>interaction_required

### <a name="issue"></a>Probléma

Hibaüzenet fogadása `interaction_required`.

### <a name="cause"></a>Ok

A rendszer szerint feltételes hozzáférési szabályzat vagy MFA vonatkozik a felhasználóra. Mivel az interaktív hitelesítés még nem támogatott, a felhasználót vagy a fürtöt ki kell venni az MFA/feltételes hozzáférés hatálya alól. Ha úgy dönt, hogy mentesíti a fürt (IP-cím `ServiceEndpoints` alapú mentességi házirend), majd győződjön meg arról, hogy az AD engedélyezve van az adott virtuális hálózat.

### <a name="resolution"></a>Megoldás:

Használjon feltételes hozzáférési házirendet, és mentesítse a HDInisght-fürtöket az MFA alól, ahogy az a [HDInsight-fürt konfigurálása vállalati biztonsági csomaggal az Azure Active Directory tartományi szolgáltatások használatával](./apache-domain-joined-configure-using-azure-adds.md)című részében látható.

---

## <a name="sign-in-denied"></a>Bejelentkezés megtagadva

### <a name="issue"></a>Probléma

A bejelentkezés megtagadva.

### <a name="cause"></a>Ok

Ahhoz, hogy ezt a szakaszt, az OAuth hitelesítés nem probléma, de Kerberos hitelesítés. Ha ezt a fürtöt az ADLS támogatja, az OAuth-bejelentkezés sikeres volt a Kerberos-hitelesítés megkísérlése előtt. WasB-fürtökön a benem i. A Kerberos-hiba számos oka lehet – például a jelszókimaradások nincsenek szinkronban, a felhasználói fiók zárolva van az Azure AD DS-ben, és így tovább. A jelszókibódák csak akkor szinkronizálódnak, ha a felhasználó megváltoztatja a jelszót. Amikor létrehozza az Azure AD DS-példányt, elindítja a létrehozás után módosított jelszavak szinkronizálását. Nem szinkronizálvisszamenőlegesen a megkezdete előtt beállított jelszavakat.

### <a name="resolution"></a>Megoldás:

Ha úgy gondolja, hogy a jelszavak nincsenek szinkronban, próbálja meg módosítani a jelszót, és várjon néhány percet a szinkronizálásra.

Próbálja meg az SSH-t egy bekell, meg kell próbálnia hitelesíteni (kinit) ugyanazzal a felhasználói hitelesítő adatokkal, egy olyan gépről, amely csatlakozik a tartományhoz. SSH a fej / él csomópont egy helyi felhasználó, majd fuss kinit.

---

## <a name="kinit-fails"></a>kinit nem sikerül

### <a name="issue"></a>Probléma

Kinit elbukik.

### <a name="cause"></a>Ok

Változik.

### <a name="resolution"></a>Megoldás:

Ahhoz, hogy a kinit `sAMAccountName` sikeres legyen, ismernie kell a (ez a rövid fiók neve a birodalom nélkül). `sAMAccountName`általában a fiók előtagja (mint a bob in `bob@contoso.com`). Egyes felhasználók számára ez más lehet. Szüksége lesz arra, hogy böngésszen / keresni a könyvtárban, hogy megtanulják a `sAMAccountName`.

A keresés `sAMAccountName`módjai:

* Ha a helyi Ambari-rendszergazda segítségével tud bejelentkezni az Ambari ba, tekintse meg a felhasználók listáját.

* Ha [tartományhoz csatlakozó Windows-gépe](../../active-directory-domain-services/manage-domain.md)van, a szokásos Windows AD eszközökkel tallózhat. Ehhez működő fiókra van szükség a tartományban.

* A fő csomópontról samba parancsokkal kereshet. Ehhez érvényes Kerberos-munkamenetre (sikeres kinit) van szükség. net ads search "(userPrincipalName=bob*)"

    A keresési / böngészési `sAMAccountName` eredmények nek meg kell mutatnia az attribútumot. Is, akkor nézd meg `pwdLastSet`más `badPasswordTime` `userPrincipalName` attribútumok, mint a , , stb, hogy ha ezek a tulajdonságok egyeznek meg, amit elvár.

---

## <a name="kinit-fails-with-preauthentication-failure"></a>a kinit sikertelen előhitelesítési hibával

### <a name="issue"></a>Probléma

Kinit sikertelen. `Preauthentication`

### <a name="cause"></a>Ok

Helytelen felhasználónév vagy jelszó.

### <a name="resolution"></a>Megoldás:

Ellenőrizze a felhasználónevét és jelszavát. Ellenőrizze a fent leírt egyéb tulajdonságokat is. A részletes hibakeresés engedélyezéséhez `export KRB5_TRACE=/tmp/krb.log` futtassa a munkamenetből a kinit kipróbálása előtt.

---

## <a name="job--hdfs-command-fails-due-to-tokennotfoundexception"></a>TokenNotFoundException miatt sikertelen a Feladat / HDFS parancs

### <a name="issue"></a>Probléma

Job / HDFS parancs `TokenNotFoundException`sikertelen miatt .

### <a name="cause"></a>Ok

A szükséges OAuth hozzáférési jogkivonat nem található a feladat / parancs sikeres. Az ADLS / ABFS illesztőprogram megpróbálja lekérni az OAuth hozzáférési jogkivonatot a hitelesítő adatok szolgáltatásból, mielőtt tárolókérelmeket. Ez a jogkivonat regisztrálva lesz, amikor bejelentkezik az Ambari portálon ugyanazzal a felhasználóval.

### <a name="resolution"></a>Megoldás:

Győződjön meg arról, hogy sikeresen bejelentkezett az Ambari portálra egyszer azon a felhasználónévn keresztül, amelynek identitását a feladat futtatásához használják.

---

## <a name="error-fetching-access-token"></a>Hiba a hozzáférési jogkivonat beolvasásakor

### <a name="issue"></a>Probléma

A felhasználó hibaüzenetet `Error fetching access token`kap.

### <a name="cause"></a>Ok

Ez a hiba időnként akkor fordul elő, amikor a felhasználók a hozzáférés-hozzáférés és a Kerberos-token lejárt használatával próbálnak hozzáférni az ADLS Gen2-hez.

### <a name="resolution"></a>Megoldás:

* Az Azure Data Lake Storage Gen1 esetében tisztítsa meg a böngésző gyorsítótárát, és jelentkezzen be újra az Ambariba.

* Az Azure Data Lake Storage `/usr/lib/hdinsight-common/scripts/RegisterKerbWithOauth.sh <upn>` Gen2 esetén futtassa azt a felhasználót, amilyenként a felhasználó megpróbál bejelentkezni

---

## <a name="next-steps"></a>További lépések

Ha nem látta a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikébe:

* Válaszokat kaphat az Azure szakértőitől az [Azure közösségi támogatásán](https://azure.microsoft.com/support/community/)keresztül.

* Lépjen [@AzureSupport](https://twitter.com/azuresupport) kapcsolatba a hivatalos Microsoft Azure-fiókkal az ügyfélélmény javítása érdekében. Az Azure-közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérelmet nyújthat be az [Azure Portalról.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Válassza a **menüsor Támogatás parancsát,** vagy nyissa meg a **Súgó + támogatási** központot. További információkért tekintse [át az Azure-támogatási kérelem létrehozása című áttekintést.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Az Előfizetés-kezelés hez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetésrészét képezi, a technikai támogatást pedig az [Azure-támogatási csomagok](https://azure.microsoft.com/support/plans/)egyike biztosítja.
