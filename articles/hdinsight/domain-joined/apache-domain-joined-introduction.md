---
title: "Hadoop-biztonság – tartományhoz csatlakoztatott HDInsight-fürtök – Azure | Microsoft Docs"
description: "Információk ...."
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7dc6847d-10d4-4b5c-9c83-cc513cf91965
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/31/2016
ms.author: saurinsh
ms.openlocfilehash: 5e0464a8f9eb29daad544a708a27107a91a72736
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="an-introduction-to-hadoop-security-with-domain-joined-hdinsight-clusters-preview"></a>Bevezetés a Hadoop-biztonság használatába a tartományhoz csatlakoztatott HDInsight-fürtök esetében (Előnézet)

Az Azure HDInsight eddig csak egyetlen felhasználói helyi rendszergazdát támogatott. Ez remekül működött a kisebb alkalmazásfejlesztő csapatoknál vagy részlegeknél. Amikor a Hadoop-alapú számítási feladatok nagyobb népszerűségre tettek szert a vállalati szektorban, egyre inkább fontosabbá váltak a vállalati szintű képességek, például az Active Directory-alapú hitelesítés, a több felhasználó támogatása és a szerepköralapú hozzáférés-vezérlés. A tartományhoz csatlakoztatott HDInsight-fürtök használatával létrehozhat egy Active Directory-tartományhoz csatlakoztatott HDInsight-fürtöt, és konfigurálhatja azon vállalati alkalmazottak listáját, akik hitelesíthetnek az Active Directoryn át a bejelentkezéshez a HDInsight-fürtbe. A vállalaton kívülről senki sem tud bejelentkezni a HDInsight-fürtbe, illetve senki sem érheti el azt onnan. A vállalati rendszergazda konfigurálhatja a szerepköralapú hozzáférés-vezérlést a Hive-biztonsághoz az [Apache Ranger](http://hortonworks.com/apache/ranger/) használatával, és így kizárólag a szükségesekre korlátozhatja a hozzáférést az adatokhoz. Végül, a rendszergazda naplózhatja az alkalmazottak adathozzáférését, és bármely változtatást a hozzáférés-vezérlés házirendjeiben, és így a vállalati erőforrások magas szintű irányítását éri el.

> [!NOTE]
> Az előnézetben leírt új funkciók kizárólag a Linux-alapú HDInsight-fürtöknél érhetők el a Hive számítási feladatokhoz. Az egyéb számítási feladatokat, például a HBase-t, a Sparkot, a Stormot és a Kafkát a jövőbeli kiadások teszi elérhetővé.

> [!IMPORTANT]
> Oozie nincs engedélyezve a HDInsight-tartományhoz.

## <a name="benefits"></a>Előnyök
A vállalati biztonság négy pillérre támaszkodik – szegélyhálózat-alapú biztonság, hitelesítés, engedélyezés és titkosítás.

![A tartományhoz csatlakoztatott HDInsight-fürtök előnyösek ezen pillérek számára](./media/apache-domain-joined-introduction/hdinsight-domain-joined-four-pillars.png).

### <a name="perimeter-security"></a>Szegélyhálózat-alapú biztonság
A HDInsight szegélyhálózat-alapú biztonságát a virtuális hálózatok és az átjáró szolgáltatás biztosítja. Manapság a vállalati rendszergazda létrehozhat egy HDInsight-fürtöt a virtuális hálózatban, és a hálózati biztonsági csoportot használhatja (bejövő vagy kimenő tűzfalszabályok) a virtuális hálózathoz való hozzáférés korlátozásához. Kizárólag a bejövő tűzfalszabályokban megadott IP-címek tudnak kommunikálni a HDInsight-fürttel, ezzel biztosítva a szegélyhálózat-alapú biztonságot. A szegélyhálózat-alapú biztonság egy másik rétege az átjáró-szolgáltatással érhető el. Az átjáró az a szolgáltatás, amely első védelmi vonalként működik a HDInsight-fürthöz érkező bármely kérés esetén. Fogadja a kéréseket, ellenőrzi, és csak az ellenőrzés után engedi tovább azokat a további csomópontokhoz a fürtben, ezzel biztosítva az egyéb, a fürtben található név- és adatcsomópontok szegélyhálózat-alapú biztonságát.

### <a name="authentication"></a>Authentication
Ezzel a nyilvános előzetes verzióval a vállalati rendszergazdák üzembe helyezhetnek egy tartományhoz csatlakoztatott HDInsight-fürtöt egy [virtuális hálózatban](https://azure.microsoft.com/services/virtual-network/). A HDInsight-fürt csomópontjai a vállalat által kezelt tartományhoz fognak kapcsolódni. Ez az [Azure Active Directory tartományi szolgáltatások](../../active-directory-domain-services/active-directory-ds-overview.md) használatával érhető el. A fürt összes csomópontja a vállalat által kezelt tartományhoz kapcsolódik. Ezzel a beállítással a vállalat alkalmazottai bejelentkezhetnek a fürt csomópontjaiba a tartományi hitelesítő adataik használatával. A tartományi hitelesítő adataikat továbbá arra is használhatják, hogy egyéb engedélyezett végpontokban hitelesítsék magukat, például a Hue, Ambari Views, ODBC, JDBC, PowerShell és REST API végpontokban, hogy kommunikáljanak a fürttel. A rendszergazda ezen végpontokon keresztül teljes mértékben vezérelheti a fürttel kommunikáló felhasználók számát.

### <a name="authorization"></a>Engedélyezés
A legtöbb vállalat által követett ajánlott eljárás az, hogy nem minden alkalmazott rendelkezik hozzáféréshez az összes vállalati erőforráshoz. Hasonlóképpen, ezzel a kiadással a rendszergazda meghatározhatja a szerepköralapú hozzáférés-vezérlés házirendjét a fürt erőforrásaihoz. Például, a rendszergazda konfigurálhatja az [Apache Ranger](http://hortonworks.com/apache/ranger/) keretrendszert, hogy beállítsa a Hive hozzáférés-vezérlés házirendjét. Ez a funkció biztosítja, hogy az alkalmazottak csak annyi adathoz férjenek hozzá, amennyi szükséges a munkájuk sikerességéhez. A fürt SSH-hozzáférését is csak a rendszergazda korlátozza.

### <a name="auditing"></a>Naplózás
A HDInsight-fürt erőforrásainak illetéktelen felhasználóktól való védelme és az adatok biztosítása mellett szükséges az összes fürterőforráshoz és adathoz történő hozzáférés naplózása is, hogy nyomon követhetőek legyenek az illetéktelen vagy nem szándékos hozzáférések az erőforrásokhoz. Ezzel az előzetes verzióval a rendszergazda megtekintheti és jelentheti az összes hozzáférést a HDInsight-fürtök erőforrásaihoz és adataihoz. Ezenkívül a rendszergazda megtekintheti és jelentheti a hozzáférés-vezérlés Apache Ranger által támogatott végpontokban végzett változtatásait. A tartományhoz csatlakoztatott HDInsight-fürt a már ismert Apache Ranger felhasználói felületet használja a keresésre a naplókban. A háttérben a Ranger az [Apache Solr](http://hortonworks.com/apache/solr/) platformot használja a tárolásra és a keresésre a naplókban.

### <a name="encryption"></a>Titkosítás
Az adatok védelme fontos a szervezeti biztonság és a megfelelési követelmények teljesítése érdekében, és az illetéktelen alkalmazottak adatokhoz való hozzáférésének korlátozása mellett ajánlott titkosítással is védeni őket. A HDInsight-fürtök, az Azure Storage-blob adattárolók, illetve az Azure Data Lake Storage is támogatja a transzparens kiszolgálóoldali inaktív [adattikosítást](../../storage/common/storage-service-encryption.md). A biztonságos HDInsight-fürtök zökkenőmentesen működnek a kiszolgálóoldali inaktív adattitkosítás funkcióval.

## <a name="next-steps"></a>Következő lépések
* A tartományhoz csatlakoztatott HDInsight-fürtök konfigurálásához lásd: [Tartományhoz csatlakoztatott HDInsight-fürtök konfigurálása](apache-domain-joined-configure.md).
* A tartományhoz csatlakoztatott HDInsight-fürtök kezeléséhhez lásd: [Tartományhoz csatlakoztatott HDInsight-fürtök kezelése](apache-domain-joined-manage.md).
* A Hive-házirendek konfigurálásához és a Hive-lekérdezések futtatásához lásd: [Hive-házirendek konfigurálása a tartományhoz csatlakoztatott HDInsight-fürtökben](apache-domain-joined-run-hive.md).
* Az SSH használata a tartományhoz csatlakoztatott HDInsight-fürtökön Hive-lekérdezéseket futtat, tekintse meg a [az SSH a Hdinsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
