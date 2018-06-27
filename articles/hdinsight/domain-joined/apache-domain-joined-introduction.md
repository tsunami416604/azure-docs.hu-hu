---
title: HDInsight - tartományhoz a HDInsight-fürtök – Azure
description: Információk ....
services: hdinsight
author: omidm1
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7dc6847d-10d4-4b5c-9c83-cc513cf91965
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: omidm
ms.openlocfilehash: 3fd3a4b8982fe2170726df03bdc884e658d0b0c2
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37019488"
---
# <a name="an-introduction-to-hadoop-security-with-domain-joined-hdinsight-clusters"></a>Tartományhoz csatlakozó HDInsight-fürtökkel a Hadoop biztonsági bemutatása

Az Azure HDInsight eddig csak egyetlen felhasználói helyi rendszergazdát támogatott. Ez remekül működött a kisebb alkalmazásfejlesztő csapatoknál vagy részlegeknél. Hadoop-alapú munkaterhelések szerzett további időben népszerűvé vált a vállalati ágazatban enterprise osztályú szolgáltatásokat, például active directory-alapú hitelesítés, szükség szerint több felhasználó támogatása, és a szerepköralapú hozzáférés-vezérlés egyre fontosabb vált. A tartományhoz csatlakoztatott HDInsight-fürtök használatával létrehozhat egy Active Directory-tartományhoz csatlakoztatott HDInsight-fürtöt, és konfigurálhatja azon vállalati alkalmazottak listáját, akik hitelesíthetnek az Active Directoryn át a bejelentkezéshez a HDInsight-fürtbe. A vállalaton kívülről senki sem tud bejelentkezni a HDInsight-fürtbe, illetve senki sem érheti el azt onnan. A vállalati rendszergazda konfigurálhat szerepköralapú hozzáférés-vezérlés Hive biztonsági használatával [Apache Pletyka](http://hortonworks.com/apache/ranger/), így való hozzáférés korlátozása csak adatokat, mint amennyit szükséges. Végül, a rendszergazda naplózhatja az alkalmazottak adathozzáférését, és bármely változtatást a hozzáférés-vezérlés házirendjeiben, és így a vállalati erőforrások magas szintű irányítását éri el.

> [!NOTE]
> A jelen cikkben ismertetett új funkciók érhetők el az előzetes verzió csak a következő fürt típusa: Hadoop, Spark és interaktív lekérdezése. Oozie engedélyezve van a tartományhoz csatlakoztatott fürtökön. Ahhoz, hogy hozzáférhessen a webes felhasználói felület felhasználók engedélyezze Oozie [bújtatás](../hdinsight-linux-ambari-ssh-tunnel.md)

## <a name="benefits"></a>Előnyök
A vállalati biztonsági négy fő oszlopok – szegélyhálózati biztonsági hitelesítési, engedélyezési és titkosítási tartalmazza.

![A tartományhoz csatlakoztatott HDInsight-fürtök előnyösek ezen pillérek számára](./media/apache-domain-joined-introduction/hdinsight-domain-joined-four-pillars.png).

### <a name="perimeter-security"></a>Szegélyhálózat-alapú biztonság
A HDInsight szegélyhálózat-alapú biztonságát a virtuális hálózatok és az átjáró szolgáltatás biztosítja. Egy vállalati rendszergazdai ma, hozzon létre egy virtuális hálózaton belüli HDInsight-fürtöt, és a hálózati biztonsági csoportok (tűzfalszabályok) használatával korlátozza a hozzáférést a virtuális hálózat. Kizárólag a bejövő tűzfalszabályokban megadott IP-címek tudnak kommunikálni a HDInsight-fürttel, ezzel biztosítva a szegélyhálózat-alapú biztonságot. A szegélyhálózat-alapú biztonság egy másik rétege az átjáró-szolgáltatással érhető el. Az átjáró egy a szolgáltatás, amely védelmet biztosít a HDInsight-fürthöz bejövő kérések az első sort. Fogadja a kéréseket, ellenőrzi, és csak az ellenőrzés után engedi tovább azokat a további csomópontokhoz a fürtben, ezzel biztosítva az egyéb, a fürtben található név- és adatcsomópontok szegélyhálózat-alapú biztonságát.

### <a name="authentication"></a>Hitelesítés
Vállalati rendszergazda létrehozhat egy tartományhoz csatlakozó HDInsight-fürtöt egy [virtuális hálózati](https://azure.microsoft.com/services/virtual-network/). A HDInsight-fürt csomópontjai a vállalat által kezelt tartományhoz fognak kapcsolódni. Ez az [Azure Active Directory tartományi szolgáltatások](../../active-directory-domain-services/active-directory-ds-overview.md) használatával érhető el. A fürt összes csomópontja a vállalat által kezelt tartományhoz kapcsolódik. Ezzel a beállítással a vállalat alkalmazottai bejelentkezhetnek a fürt csomópontjaiba a tartományi hitelesítő adataik használatával. A tartományi hitelesítő adataik más jóváhagyott végpontokon, mint az Ambari nézetek, ODBC, JDBC, PowerShell és a REST API-k amellyel kommunikálni tud a fürt hitelesítéséhez szolgáltatást is alkalmazhatja. A rendszergazda ezen végpontokon keresztül teljes mértékben vezérelheti a fürttel kommunikáló felhasználók számát.

### <a name="authorization"></a>Engedélyezés
A legtöbb vállalat által követett ajánlott eljárás az, hogy nem minden alkalmazott rendelkezik hozzáféréshez az összes vállalati erőforráshoz. Hasonlóképpen a ebben a kiadásban a rendszergazda segítségével megadhatja a fürt erőforrásait a szerepköralapú hozzáférés-vezérlési házirendeket. Például, a rendszergazda konfigurálhatja az [Apache Ranger](http://hortonworks.com/apache/ranger/) keretrendszert, hogy beállítsa a Hive hozzáférés-vezérlés házirendjét. Ez a funkció biztosítja, hogy az alkalmazottak csak annyi adathoz férjenek hozzá, amennyi szükséges a munkájuk sikerességéhez. A fürt SSH-hozzáférését is csak a rendszergazda korlátozza.

### <a name="auditing"></a>Naplózás
A HDInsight-fürt erőforrásainak illetéktelen felhasználóktól való védelme és az adatok biztosítása mellett szükséges az összes fürterőforráshoz és adathoz történő hozzáférés naplózása is, hogy nyomon követhetőek legyenek az illetéktelen vagy nem szándékos hozzáférések az erőforrásokhoz. A rendszergazda megtekintheti, és a HDInsight fürt erőforrásokhoz és adatokhoz való hozzáférésének jelentést. Ezenkívül a rendszergazda megtekintheti és jelentheti a hozzáférés-vezérlés Apache Ranger által támogatott végpontokban végzett változtatásait. A tartományhoz csatlakoztatott HDInsight-fürt a már ismert Apache Ranger felhasználói felületet használja a keresésre a naplókban. A háttérben a Ranger az [Apache Solr](http://hortonworks.com/apache/solr/) platformot használja a tárolásra és a keresésre a naplókban.

### <a name="encryption"></a>Titkosítás
Az adatok védelme fontos a szervezeti biztonság és a megfelelési követelmények teljesítése érdekében, és az illetéktelen alkalmazottak adatokhoz való hozzáférésének korlátozása mellett ajánlott titkosítással is védeni őket. A HDInsight-fürtök, az Azure Storage-blob adattárolók, illetve az Azure Data Lake Storage is támogatja a transzparens kiszolgálóoldali inaktív [adattikosítást](../../storage/common/storage-service-encryption.md). Biztonságos HDInsight-fürtök problémamentesen működik a kiszolgálóoldali titkosítás az adatok a többi funkció.

## <a name="next-steps"></a>További lépések
* [Tervezze meg a tartományhoz a HDInsight-fürtök](apache-domain-joined-architecture.md).
* [Konfigurálja a tartományhoz a HDInsight-fürtök](apache-domain-joined-configure.md).
* [Tartományhoz csatlakozó HDInsight-fürtök kezelése](apache-domain-joined-manage.md).
* [A tartományhoz a HDInsight-fürtök Hive-szabályzatok konfigurálása](apache-domain-joined-run-hive.md).
* [Az SSH használata a HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).

