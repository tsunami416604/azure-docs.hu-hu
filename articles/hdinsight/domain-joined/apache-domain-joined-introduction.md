---
title: Hadoop-biztonság használatába a tartományhoz csatlakoztatott Azure HDInsight-fürtök bemutatása
description: Ismerje meg, hogyan tartományhoz csatlakoztatott Azure HDInsight-fürtök támogatják a vállalati biztonság négy alappillérét.
services: hdinsight
ms.service: hdinsight
author: omidm1
ms.author: omidm
editor: jasonwhowell
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/26/2018
ms.openlocfilehash: e697b0ffe7cde65cd47a2f1b1db5b544b6dc1d3c
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2018
ms.locfileid: "39595630"
---
# <a name="an-introduction-to-hadoop-security-with-domain-joined-hdinsight-clusters"></a>Hadoop-biztonság használatába a tartományhoz csatlakoztatott HDInsight-fürtök bemutatása

Múltbeli időpont, az Azure HDInsight támogatott csak egyetlen felhasználó: helyi rendszergazdaként. Ez remekül működött a kisebb alkalmazásfejlesztő csapatoknál vagy részlegeknél. Mivel a Hadoop-alapú számítási feladatok nagyobb népszerűségre tettek a vállalati szektorban, szükség van a nagyvállalati szintű szolgáltatásokat, például az Active Directory-alapú hitelesítés, többfelhasználós támogatja, és a szerepköralapú hozzáférés-vezérlés egyre inkább fontosabbá váltak. 

Egy Active Directory-tartományhoz csatlakozó HDInsight-fürtöt hozhat létre. Konfigurálhatja a vállalati alkalmazottak, akik hitelesíthetnek az Azure Active Directoryban való bejelentkezéshez a HDInsight-fürt listáját. Jelentkezzen be, vagy a HDInsight-fürt eléréséhez a vállalaton kívüli személyek nem. 

A vállalati rendszergazda konfigurálhatja szerepköralapú hozzáférés-vezérlés (RBAC) a Hive-biztonság használatával [Apache Ranger](http://hortonworks.com/apache/ranger/). RBAC konfigurálása korlátozza az adatok elérése csak akkor szükséges. Végül a rendszergazda naplózhatja az alkalmazottak és bármely változtatást a hozzáférés-vezérlési házirendeket adatelérési. A rendszergazda egy magas szintű a vállalati erőforrásokat, majd érheti el.

> [!NOTE]
> Az ebben a cikkben leírt új funkciók érhetők el a következő típusú fürtöket csak az előzetes verzióban érhető el: Hadoop, Spark- és interaktív lekérdezés. Az Oozie engedélyezve van a tartományhoz csatlakoztatott fürtök. Hozzáférhet az Oozie webes felület, engedélyezze a felhasználók [tunneling](../hdinsight-linux-ambari-ssh-tunnel.md).

Vállalati biztonság négy fő alappillérét tartalmazza: szegélyhálózat-alapú biztonság, hitelesítés, engedélyezés és titkosítás.

![Tartományhoz csatlakoztatott HDInsight-fürtök a vállalati biztonság négy alappillérét a előnyei](./media/apache-domain-joined-introduction/hdinsight-domain-joined-four-pillars.png).

## <a name="perimeter-security"></a>Szegélyhálózat-alapú biztonság
A HDInsight szegélyhálózat-alapú biztonság virtuális hálózatok és az Azure VPN Gateway szolgáltatás keresztül érhető el. Vállalati rendszergazda létrehozhat egy HDInsight-fürt virtuális hálózaton belül és használja a hálózati biztonsági csoportok (tűzfalszabályok) a virtuális hálózathoz való hozzáférés korlátozásához. Csak a bejövő tűzfalszabályokban megadott IP-címeket fog tudni kommunikálni a HDInsight-fürt. Ez a konfiguráció a szegélyhálózat-alapú biztonságot nyújt.

Szegélyhálózat-alapú biztonsági réteget a VPN-átjáró szolgáltatáson keresztül érhető el. Az átjáró védelmet biztosít a HDInsight-fürthöz érkező bármely kérés az első sorában funkcionál. Azt fogadja a kéréseket, ellenőrzi, és csak ezután lehetővé teszi, hogy a kérelem átadása a fürt más csomópontjaira. Ezzel a módszerrel az átjáró biztosítja a fürt más név- és adatcsomópontok szegélyhálózat-alapú biztonságát.

## <a name="authentication"></a>Hitelesítés
Vállalati rendszergazda létrehozhat egy tartományhoz csatlakoztatott HDInsight-fürtön az egy [virtuális hálózat](https://azure.microsoft.com/services/virtual-network/). A HDInsight-fürt összes csomópontján csatlakozott a tartományhoz, a vállalat által kezelt. Ez a gazdafájlon keresztül a [Azure Active Directory Domain Services](../../active-directory-domain-services/active-directory-ds-overview.md). 

Ezzel a beállítással vállalat alkalmazottai bejelentkezhet a fürtcsomópontok tartományi hitelesítő adataik használatával. Tartományi hitelesítő adataik egyéb engedélyezett végpontokban, például az Ambari Views, ODBC, JDBC, PowerShell és REST API-k kommunikáljanak a fürttel való hitelesítéshez szolgáltatást is alkalmazhatja. A rendszergazda teljes mértékben vezérelheti a felhasználók száma, akik a fürt ezen végpontokon keresztül kommunikálhat.

## <a name="authorization"></a>Engedélyezés
Amely a legtöbb vállalat kövesse az ajánlott eljárás, hogy így arról, hogy nem minden alkalmazott rendelkezik-e az összes vállalati erőforrásokhoz való hozzáférést. Hasonlóképpen a rendszergazda meghatározhatja a szerepköralapú hozzáférés-vezérlési házirendeket, a fürt erőforrásaihoz. 

Például, a rendszergazda konfigurálhatja az [Apache Ranger](http://hortonworks.com/apache/ranger/) keretrendszert, hogy beállítsa a Hive hozzáférés-vezérlés házirendjét. Ez a funkció biztosítja, hogy alkalmazottai érhessék el csak annyi adathoz kell lenniük a munkájuk sikerességéhez. A fürthöz SSH-hozzáférés csak a rendszergazda is korlátozódik.

## <a name="auditing"></a>Naplózás
A fürt erőforrásainak és az adatok hozzáférésének naplózása szükség, nyomon követéséhez az illetéktelen vagy nem szándékos hozzáférések az erőforrásokhoz. Olyan fontos, mint a HDInsight-fürt erőforrásainak védelme a jogosulatlan felhasználók és az adatok védelme. 

A rendszergazda megtekintheti és jelentheti az összes hozzáférést a HDInsight-fürt erőforrásainak és az adatokat. A rendszergazda megtekintheti és jelentheti a hozzáférés-vezérlési házirendeket, az Apache Ranger által támogatott végpontokban létrehozott összes módosítást. 

A tartományhoz csatlakoztatott HDInsight-fürt a jól ismert Apache Ranger felhasználói Felületet használja a keresésre a naplókban. A háttérben a Ranger használ [Apache Solr](http://hortonworks.com/apache/solr/) tárolására, és a keresésre a naplókban.

## <a name="encryption"></a>Titkosítás
Az adatok védelmének fontos értekezlet szervezeti biztonsági és megfelelőségi követelményeknek. Adatok elérésének korlátozásával illetéktelen alkalmazottak, együtt kell titkosítás. 

A HDInsight-fürtök az Azure Blob storage és az Azure Data Lake Storage Gen1 – támogatási transzparens kiszolgálóoldali mindkét adattárak [az adatok titkosítása az](../../storage/common/storage-service-encryption.md) inaktív. Biztonságos HDInsight-fürtök zökkenőmentesen működnek a kiszolgálóoldali titkosítást az adatok képesség inaktív.

## <a name="next-steps"></a>További lépések
* [Tartományhoz csatlakoztatott HDInsight-fürtök tervezése](apache-domain-joined-architecture.md)
* [Tartományhoz csatlakoztatott HDInsight-fürtök konfigurálása](apache-domain-joined-configure.md)
* [Tartományhoz csatlakoztatott HDInsight-fürtök kezelése](apache-domain-joined-manage.md)
* [Hive-házirendek a tartományhoz csatlakoztatott HDInsight-fürtök konfigurálása](apache-domain-joined-run-hive.md)
* [SSH használata a HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)

