---
title: Felügyelt identitások az Azure HDInsightban
description: Áttekintést nyújt a felügyelt identitások az Azure HDInsight ban megvalósítása.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/20/2019
ms.openlocfilehash: daae9c16797ad9c1b85635f5aec7d0cf884e003f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206010"
---
# <a name="managed-identities-in-azure-hdinsight"></a>Felügyelt identitások az Azure HDInsightban

A felügyelt identitás az Azure Active Directoryban (Azure AD) regisztrált identitás, amelynek hitelesítő adatait az Azure kezeli. Felügyelt identitások használatával nem kell regisztrálnia a szolgáltatásnévtagok at Azure AD, vagy a hitelesítő adatok, például a tanúsítványok karbantartása.

Felügyelt identitások azure HDInsight azure AD tartományi szolgáltatások eléréséhez vagy az Azure Data Lake Storage Gen2 fájlok eléréséhez az Azure Data Lake Storage Gen2 szükség esetén.

A felügyelt identitásoknak két típusa van: felhasználó által hozzárendelt és rendszerhez rendelt. Az Azure HDInsight csak a felhasználó által hozzárendelt felügyelt identitásokat támogatja. A HDInsight nem támogatja a rendszerhez rendelt felügyelt identitásokat. A felhasználó által hozzárendelt felügyelt identitás önálló Azure-erőforrásként jön létre, amelyet ezután hozzárendelhet egy vagy több Azure-szolgáltatáspéldányhoz. Ezzel szemben egy rendszer által hozzárendelt felügyelt identitás jön létre az Azure AD-ben, majd automatikusan engedélyezve egy adott Azure-szolgáltatáspéldány. A rendszer által hozzárendelt felügyelt identitás élettartama ezután az engedélyezett szolgáltatáspéldány élettartamához van kötve.

## <a name="hdinsight-managed-identity-implementation"></a>HDInsight felügyelt identitásmegvalósítás

Az Azure HDInsightban felügyelt identitások vannak kiépítve a fürt minden csomópontján. Ezek az identitás-összetevők azonban csak a HDInsight szolgáltatás által használható. Jelenleg nincs támogatott módszer a hdinsight-fürtcsomópontokon telepített felügyelt identitások használatával hozzáférési jogkivonatok létrehozásához. Egyes Azure-szolgáltatások felügyelt identitások egy olyan végponttal vannak megvalósítva, amely segítségével hozzáférési jogkivonatokat szerezhet be más Azure-szolgáltatásokkal való egyedüli együttműködéshez.

## <a name="create-a-managed-identity"></a>Felügyelt identitás létrehozása

Felügyelt identitások az alábbi módszerek bármelyikével hozhatók létre:

* [Azure-portál](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* [Azure Resource Manager](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)
* [Azure CLI](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)

A felügyelt identitás konfigurálásának további lépései attól függnek, hogy hol fogja használni.

## <a name="managed-identity-scenarios-in-azure-hdinsight"></a>Felügyelt identitásforgatókönyvek az Azure HDInsightban

Felügyelt identitások az Azure HDInsight több forgatókönyv esetén is használhatók. A részletes beállítási és konfigurációs utasításokat a kapcsolódó dokumentumokban találja:

* [2. generációs Azure Data Lake Storage](hdinsight-hadoop-use-data-lake-storage-gen2.md#create-a-user-assigned-managed-identity)
* [Enterprise Security Package](domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-and-authorize-a-managed-identity)
* [Ügyfél által felügyelt kulcson alapuló lemeztitkosítás](disk-encryption.md)

## <a name="faq"></a>GYIK
### <a name="what-happens-if-i-delete-the-managed-identity-after-the-cluster-creation"></a>Mi történik, ha a fürt létrehozása után törlöm a felügyelt identitást?
A fürt problémákba ütközik, amikor a felügyelt identitásra van szükség. A fürt létrehozása után jelenleg nincs mód a felügyeleti identitás frissítésére vagy módosítására. Ezért azt javasoljuk, hogy győződjön meg arról, hogy a felügyelt identitás nem törlődik a fürt futásideje alatt. Másik lehetőségként újra létrehozhatja a fürtöt, és új felügyelt identitást rendelhet hozzá.

## <a name="next-steps"></a>További lépések

* [Mit kell tudni az Azure-erőforrások felügyelt identitásairól?](../active-directory/managed-identities-azure-resources/overview.md)
