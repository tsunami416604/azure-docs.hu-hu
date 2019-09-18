---
title: Felügyelt identitások az Azure HDInsight
description: Áttekintést nyújt a felügyelt identitások megvalósításáról az Azure HDInsight-ben.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: 02ea164a1fa29b494801623d418be73fc47d069c
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71077082"
---
# <a name="managed-identities-in-azure-hdinsight"></a>Felügyelt identitások az Azure HDInsight

A felügyelt identitás a Azure Active Directory (Azure AD) szolgáltatásban regisztrált identitás, amelynek hitelesítő adatait az Azure felügyeli. A felügyelt identitások esetében nem kell regisztrálnia az Azure AD szolgáltatásban, vagy a hitelesítő adatokat, például a tanúsítványokat kell fenntartania.

A felügyelt identitások használhatók az Azure HDInsight, így a fürtök hozzáférhetnek az Azure AD tartományi szolgáltatásokhoz, hozzáférhetnek Azure Key Vaulthoz, vagy hozzáférhetnek a Azure Data Lake Storage Gen2 található fájlokhoz.

A felügyelt identitások két típusa létezik: felhasználó által hozzárendelt és rendszerhez rendelt. Az Azure HDInsight felhasználó által hozzárendelt felügyelt identitásokat használ. A felhasználó által hozzárendelt felügyelt identitás önálló Azure-erőforrásként jön létre, amelyet aztán egy vagy több Azure-szolgáltatási példányhoz rendelhet hozzá. Ezzel szemben egy rendszerhez rendelt felügyelt identitás jön létre az Azure AD-ben, majd közvetlenül egy adott Azure-szolgáltatási példányon automatikusan engedélyezve lesz. A rendszer által hozzárendelt felügyelt identitás élettartama ezután a szolgáltatás azon példányának élettartamához kötődik, amelyen engedélyezve van.

## <a name="hdinsight-managed-identity-implementation"></a>HDInsight felügyelt identitás implementálása

Az Azure HDInsight-ben a felügyelt identitások kiosztása a fürt minden egyes csomópontján történik. Ezek az identitás-összetevők azonban csak a HDInsight szolgáltatás által használhatók. A HDInsight telepített felügyelt identitások használatával jelenleg nincs támogatott módszer a hozzáférési tokenek létrehozásához. Egyes Azure-szolgáltatások esetében a felügyelt identitások egy végponttal valósulnak meg, amellyel hozzáférési jogkivonatok szerezhetők be a többi Azure-szolgáltatással való interakcióhoz.

## <a name="create-a-managed-identity"></a>Felügyelt identitás létrehozása

A felügyelt identitások a következő módszerek bármelyikével hozhatók létre:

* [Azure Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* [Azure Resource Manager](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)
* [Azure CLI](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)

A felügyelt identitás konfigurálásának hátralévő lépései attól függnek, hogy milyen helyzetben lesz használatban.

## <a name="managed-identity-scenarios-in-azure-hdinsight"></a>Felügyelt identitási forgatókönyvek az Azure HDInsight

A felügyelt identitásokat több forgatókönyvben használják az Azure HDInsight. A részletes beállítási és konfigurációs utasításokért tekintse meg a kapcsolódó dokumentumokat:

* [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md#create-a-user-assigned-managed-identity)
* [Enterprise Security Package](domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-and-authorize-a-managed-identity)
* [Kafka-Bring Your Own Key (BYOK)](kafka/apache-kafka-byok.md#get-started-with-byok)

## <a name="next-steps"></a>További lépések

* [Mit kell tudni az Azure-erőforrások felügyelt identitásairól?](../active-directory/managed-identities-azure-resources/overview.md)
