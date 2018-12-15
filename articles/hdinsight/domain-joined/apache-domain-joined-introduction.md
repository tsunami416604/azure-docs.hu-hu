---
title: Bevezetés az Apache Hadoop-biztonság, a vállalati biztonsági csomag
description: Ismerje meg, hogy a vállalati biztonsági csomag hogyan támogatja a vállalati biztonság négy alappillérét.
services: hdinsight
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: b120d50b6b0f72b5977d238866cfdf26fd9be5ff
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2018
ms.locfileid: "53436891"
---
# <a name="an-introduction-to-apache-hadoop-security-with-enterprise-security-package"></a>Bevezetés az Apache Hadoop-biztonság, a vállalati biztonsági csomag

Múltbeli időpont, az Azure HDInsight támogatott csak egyetlen felhasználó: helyi rendszergazdaként. Ez remekül működött a kisebb alkalmazásfejlesztő csapatoknál vagy részlegeknél. Ahogy az Apache Hadoop-alapú számítási feladatok nagyobb népszerűségre tettek a vállalati szektorban, szükség van a nagyvállalati szintű szolgáltatásokat, például az Active Directory-alapú hitelesítéshez, többfelhasználós támogatja, és a szerepköralapú hozzáférés-vezérlés egyre inkább fontosabbá váltak. 

Létrehozhat egy HDInsight-fürtöt a vállalati biztonsági csomag (ESP), amely egy Active Directory-tartományhoz csatlakozik. Konfigurálhatja a vállalati alkalmazottak, akik hitelesíthetnek az Azure Active Directoryban való bejelentkezéshez a HDInsight-fürt listáját. Egyetlen a vállalaton kívülre jelentkezzen be, vagy a HDInsight-fürt eléréséhez. 

A vállalati rendszergazda konfigurálhatja szerepköralapú hozzáférés-vezérlés (RBAC) az Apache Hive-biztonsághoz használatával [Apache Ranger](https://hortonworks.com/apache/ranger/). RBAC konfigurálása korlátozza az adatok elérése csak akkor szükséges. Végül a rendszergazda naplózhatja az alkalmazottak és bármely változtatást a hozzáférés-vezérlési házirendeket adatelérési. A rendszergazda egy magas szintű a vállalati erőforrásokat, majd érheti el.

> [!NOTE]  
> Az Apache Oozie engedélyezve van a ESP-fürtökön. Hozzáférhet az Oozie webes felület, engedélyezze a felhasználók [tunneling](../hdinsight-linux-ambari-ssh-tunnel.md).

Vállalati biztonság négy fő alappillérét tartalmazza: szegélyhálózat-alapú biztonság, hitelesítés, engedélyezés és titkosítás.

![A vállalati biztonság négy alappillérét vállalati biztonsági csomag HDInsight-fürtök előnyei](./media/apache-domain-joined-introduction/hdinsight-domain-joined-four-pillars.png).

## <a name="perimeter-security"></a>Szegélyhálózat-alapú biztonság
A HDInsight szegélyhálózat-alapú biztonság virtuális hálózatok és az Azure VPN Gateway szolgáltatás keresztül érhető el. Vállalati rendszergazda létrehozhat egy ESP-fürt virtuális hálózaton belül és használja a hálózati biztonsági csoportok (tűzfalszabályok) a virtuális hálózathoz való hozzáférés korlátozásához. Csak a bejövő tűzfalszabályokban megadott IP-címeket fog tudni kommunikálni a HDInsight-fürt. Ez a konfiguráció a szegélyhálózat-alapú biztonságot nyújt.

Szegélyhálózat-alapú biztonsági réteget a VPN-átjáró szolgáltatáson keresztül érhető el. Az átjáró védelmet biztosít a HDInsight-fürthöz érkező bármely kérés az első sorában funkcionál. Azt fogadja a kéréseket, ellenőrzi, és csak ezután lehetővé teszi, hogy a kérelem átadása a fürt más csomópontjaira. Ezzel a módszerrel az átjáró biztosítja a fürt más név- és adatcsomópontok szegélyhálózat-alapú biztonságát.

## <a name="authentication"></a>Hitelesítés
Vállalati rendszergazda és az ESP hozhat létre egy HDInsight-fürtöt egy [virtuális hálózat](https://azure.microsoft.com/services/virtual-network/). A HDInsight-fürt összes csomópontján csatlakozott a tartományhoz, a vállalat által kezelt. Ez a gazdafájlon keresztül a [Azure Active Directory Domain Services](../../active-directory-domain-services/active-directory-ds-overview.md). 

Ezzel a beállítással vállalat alkalmazottai bejelentkezhet a fürtcsomópontok tartományi hitelesítő adataik használatával. Tartományi hitelesítő adataik egyéb engedélyezett végpontokban, mint például az Apache Ambari Views, ODBC, JDBC, PowerShell és REST API-k kommunikáljanak a fürttel való hitelesítéshez szolgáltatást is alkalmazhatja. A rendszergazda teljes mértékben vezérelheti a felhasználók száma, akik a fürt ezen végpontokon keresztül kommunikálhat.

## <a name="authorization"></a>Engedélyezés
Amely a legtöbb vállalat kövesse az ajánlott eljárás, hogy így arról, hogy nem minden alkalmazott rendelkezik-e az összes vállalati erőforrásokhoz való hozzáférést. Hasonlóképpen a rendszergazda meghatározhatja a szerepköralapú hozzáférés-vezérlési házirendeket, a fürt erőforrásaihoz. 

Például, a rendszergazda konfigurálhatja az [Apache Ranger](https://hortonworks.com/apache/ranger/) keretrendszert, hogy beállítsa a Hive hozzáférés-vezérlés házirendjét. Ez a funkció biztosítja, hogy alkalmazottai érhessék el csak annyi adathoz kell lenniük a munkájuk sikerességéhez. A fürthöz SSH-hozzáférés csak a rendszergazda is korlátozódik.

## <a name="auditing"></a>Naplózás
A fürt erőforrásainak és az adatok hozzáférésének naplózása szükség, nyomon követéséhez az illetéktelen vagy nem szándékos hozzáférések az erőforrásokhoz. Olyan fontos, mint a HDInsight-fürt erőforrásainak védelme a jogosulatlan felhasználók és az adatok védelme. 

A rendszergazda megtekintheti és jelentheti az összes hozzáférést a HDInsight-fürt erőforrásainak és az adatokat. A rendszergazda megtekintheti és jelentheti a hozzáférés-vezérlési házirendeket, az Apache Ranger által támogatott végpontokban létrehozott összes módosítást. 

ESP HDInsight fürtök a jól ismert Apache Ranger felhasználói Felületet használja a keresésre a naplókban. A háttérben a Ranger használ [Apache Solr](https://hortonworks.com/apache/solr/) tárolására, és a keresésre a naplókban.

## <a name="encryption"></a>Titkosítás
Az adatok védelmének fontos értekezlet szervezeti biztonsági és megfelelőségi követelményeknek. Adatok elérésének korlátozásával illetéktelen alkalmazottak, együtt kell titkosítás. 

A HDInsight-fürtök az Azure Blob storage és az Azure Data Lake Storage Gen1 – támogatási transzparens kiszolgálóoldali mindkét adattárak [az adatok titkosítása az](../../storage/common/storage-service-encryption.md) inaktív. Biztonságos HDInsight-fürtök zökkenőmentesen működnek a kiszolgálóoldali titkosítást az adatok képesség inaktív.

## <a name="next-steps"></a>További lépések

* [ESP HDInsight-fürtök tervezése](apache-domain-joined-architecture.md)
* [ESP HDInsight-fürtök konfigurálása](apache-domain-joined-configure.md)
* [ESP a HDInsight-fürtök kezelése](apache-domain-joined-manage.md)
* [ESP az Apache Hive-házirendek a HDInsight-fürtök konfigurálása](apache-domain-joined-run-hive.md)
* [SSH használata a HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)

