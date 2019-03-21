---
title: Az Azure HDInsight a felügyelt identitásokból
description: Az Azure HDInsight felügyelt identitások megvalósítása áttekintést nyújt.
services: hdinsight
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: hrasheed
ms.openlocfilehash: 9386bcb8e455bff5ceed1fccdf55874caf7b6507
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58175785"
---
# <a name="managed-identities-in-azure-hdinsight"></a>Az Azure HDInsight a felügyelt identitásokból

Egy felügyelt identitás regisztrálva az Azure Active Directory (Azure AD) hitelesítő adatokkal rendelkező Azure által felügyelt identitást. A felügyelt identitásokból nem kell regisztrálni az egyszerű szolgáltatások Azure AD-ben vagy a hitelesítő adatok, például a tanúsítványok kezelése.

Felügyelt identitások segítségével az Azure HDInsight-fürtök az Azure AD tartományi szolgáltatások Azure Key Vault eléréséhez, vagy az Azure Data Lake Storage Gen2 lévő fájlok eléréséhez engedélyezése.

Felügyelt identitások két típusa van: felhasználó által hozzárendelt, és a rendszer által hozzárendelt. Az Azure HDInsight a felhasználó által hozzárendelt felügyelt identitásokat használ. Önálló Azure-erőforrás, amely egy vagy több Azure szolgáltatáspéldányok majd hozzárendelheti egy felhasználó által hozzárendelt felügyelt identitás jön létre. Ezzel szemben a rendszer által hozzárendelt felügyelt identitás, az Azure ad-ben és a majd közvetlenül az egy adott Azure-példány automatikusan engedélyezve. A rendszer által hozzárendelt felügyelt identitás élettartama majd vannak kötve, a szolgáltatás példánya, amely engedélyezve van a élettartama.

## <a name="hdinsight-managed-identity-implementation"></a>A HDInsight által felügyelt identitást használó

Az Azure HDInsight, a felügyelt identitásokból a fürt minden csomópontján üzembe helyezve. Ezek identitás-összetevők, azonban olyan csak a HDInsight szolgáltatás által használható. Jelenleg nem támogatott metódust, hogy a HDInsight-fürtcsomóponton telepítve felügyelt identitások használatával hozzáférési jogkivonatokat hoz létre. Az egyes Azure-szolgáltatásokhoz a felügyelt identitásokból olyan végponttal, amely más Azure-szolgáltatások saját folytatott interakcióra szolgáló hozzáférési jogkivonatok beszerzésére használható vannak megvalósítva.

## <a name="create-a-managed-identity"></a>Hozzon létre egy felügyelt identitás

Felügyelt identitások és a következő módszerekkel hozhatók létre:

* [Azure Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* [Azure Resource Manager](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)
* [Azure CLI](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)

A felügyelt identitásnak a hátralévő lépéseket a forgatókönyvet, ahol a rendszer azt használja függenek.

## <a name="managed-identity-scenarios-in-azure-hdinsight"></a>Az Azure HDInsight felügyelt identitás forgatókönyvek

Felügyelt identitások Azure HDInsight számos módja létezik használhatók. A részletes telepítési és konfigurációs útmutatás kapcsolódó dokumentumokban talál:

* [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md#create-a-user-managed-identity)
* [Enterprise Security Package](domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-and-authorize-a-managed-identity)
* [A Kafka Bring Your Own Key (BYOK)](kafka/apache-kafka-byok.md#get-started-with-byok)

## <a name="next-steps"></a>További lépések

* [Mit kell tudni az Azure-erőforrások felügyelt identitásairól?](../active-directory/managed-identities-azure-resources/overview.md)