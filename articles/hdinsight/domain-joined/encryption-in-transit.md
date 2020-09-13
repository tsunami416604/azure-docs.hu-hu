---
title: Azure HDInsight-titkosítás átvitel közben
description: Ismerje meg, hogy az Azure HDInsight-fürtön milyen biztonsági funkciókat biztosít a titkosítás továbbítása.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/24/2020
ms.openlocfilehash: 25e38beb561ee954db2987643775f3a3c6e05737
ms.sourcegitcommit: 5d7f8c57eaae91f7d9cf1f4da059006521ed4f9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2020
ms.locfileid: "89668768"
---
# <a name="ipsec-encryption-in-transit-for-azure-hdinsight"></a>IPSec-titkosítás az Azure HDInsight

Ez a cikk az Azure HDInsight-fürtcsomópontok közötti kommunikációhoz használt titkosítás implementációját ismerteti.

> [!Note]
> Az átvitel közbeni titkosítás jelenleg a következő régiókban engedélyezett: USA keleti régiója, USA déli középső régiója, Nyugat-RÉGIÓJA. 

## <a name="background"></a>Háttér

Az Azure HDInsight különféle biztonsági funkciókat kínál a vállalati adatai biztonságossá tételéhez. Ezek a megoldások a szegélyhálózati biztonság, a hitelesítés, az engedélyezés, a naplózás, a titkosítás és a megfelelőség pillérei szerint vannak csoportosítva. A titkosítás a REST és az átvitel során egyaránt alkalmazható az adatokra.

A REST-titkosítást az Azure Storage-fiókok kiszolgálóoldali titkosítása, valamint a HDInsight-fürt részét képező Azure-beli virtuális gépek lemezes titkosítása szabályozza.

A HDInsight-on átvitt adatforgalom titkosítása [Transport Layer Security (TLS) protokollal](https://docs.microsoft.com/azure/hdinsight/transport-layer-security) történik a accssing és a fürt csomópontjai közötti [Internet Protocol biztonság (IPSec)](https://en.wikipedia.org/wiki/IPsec) érdekében. Az IPSec opcionálisan engedélyezhető a főcsomópontok, a feldolgozó csomópontok, a peremhálózati csomópontok és a Zookeeper csomópontok között. Nincs engedélyezve az átjáró vagy az [azonosító-átvitelszervező](https://docs.microsoft.com/azure/hdinsight/domain-joined/identity-broker) csomópontok közötti forgalom, amely a Windows-alapú virtuális gépek és a fürt más Linux-alapú csomópontjai között van.

## <a name="enable-encryption-in-transit"></a>Titkosítás engedélyezése az átvitelben

### <a name="azure-portal"></a>Azure Portal

Ha olyan új fürtöt szeretne létrehozni, amely a Azure Portal használatával engedélyezte a titkosítást, hajtsa végre a következő lépéseket:

1. Indítsa el a fürt normál létrehozási folyamatát. Lásd: [Linux-alapú fürtök létrehozása a HDInsight-ben a](../hdinsight-hadoop-create-linux-clusters-portal.md) fürt kezdeti létrehozási lépéseinek Azure Portal használatával.
1. Fejezze be az **alapok** és a **tároló** lapokat. Folytassa a **Biztonság + hálózatkezelés** lappal.

    :::image type="content" source="media/encryption-in-transit/create-cluster-security-networking-tab.png" alt-text="Hozzon létre egy fürt – biztonság és hálózatkezelés lapot.":::

1. A **Biztonság és hálózatkezelés** lapon kattintson a **titkosítás engedélyezése a továbbításkor** jelölőnégyzetre.

    :::image type="content" source="media/encryption-in-transit/enable-encryption-in-transit.png" alt-text="Fürt létrehozása – engedélyezze a titkosítást az átvitelben.":::

### <a name="create-a-cluster-with-encryption-in-transit-enabled-through-the-azure-cli"></a>Az Azure CLI-n keresztül engedélyező, titkosítással rendelkező fürt létrehozása

Az átvitel közbeni titkosítás engedélyezve van a `isEncryptionInTransitEnabled` tulajdonság használatával.

[Letöltheti a minta sablon és a paraméter fájlját](https://github.com/Azure-Samples/hdinsight-enterprise-security). A sablon és az Azure CLI-kódrészlet használata előtt cserélje le a következő helyőrzőket a megfelelő értékekre:

| Helyőrző | Description |
|---|---|
| `<SUBSCRIPTION_ID>` | Az Azure-előfizetés azonosítója |
| `<RESOURCE_GROUP>` | Az az erőforráscsoport, amelyben létre szeretné hozni az új fürtöt és a Storage-fiókot. |
| `<STORAGEACCOUNTNAME>` | A fürthöz használni kívánt meglévő Storage-fiók. A névnek a formának kell lennie `ACCOUNTNAME.blob.core.windows.net` |
| `<CLUSTERNAME>` | A HDInsight-fürt neve. |
| `<PASSWORD>` | Az SSH-val és a Ambari-irányítópulttal a fürtbe való bejelentkezéshez választott jelszava. |
| `<VNET_NAME>` | A virtuális hálózat, amelyben a fürt üzembe lesz helyezve. |

Az alábbi kódrészlet a következő kezdeti lépéseket hajtja végre:

1. Bejelentkezik az Azure-fiókjába.
1. Beállítja azt az aktív előfizetést, amelyben a létrehozási műveletek lesznek végrehajtva.
1. Létrehoz egy új erőforráscsoportot az új központi telepítési tevékenységekhez.
1. A sablon üzembe helyezésével hozzon létre egy új fürtöt.

```azurecli
az login
az account set --subscription <SUBSCRIPTION_ID>

# Create resource group
az group create --name <RESOURCEGROUPNAME> --location eastus2

az group deployment create --name HDInsightEnterpriseSecDeployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file hdinsight-enterprise-security.json \
    --parameters parameters.json
```

## <a name="next-steps"></a>Következő lépések

* [A vállalati biztonság áttekintése az Azure HDInsight](hdinsight-security-overview.md)
* [Azure Active Directory felhasználók szinkronizálása egy HDInsight-fürttel](../disk-encryption.md).
