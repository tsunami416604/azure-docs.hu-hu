---
title: Felügyelt identitások az Azure HDInsight
description: Áttekintést nyújt a felügyelt identitások megvalósításáról az Azure HDInsight-ben.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/15/2020
ms.openlocfilehash: 07a8c26f7fc314680c51270ebafe03d4e3a84757
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2020
ms.locfileid: "88749855"
---
# <a name="managed-identities-in-azure-hdinsight"></a>Felügyelt identitások az Azure HDInsight

A felügyelt identitás a Azure Active Directory (Azure AD) szolgáltatásban regisztrált identitás, amelynek hitelesítő adatait az Azure felügyeli. A felügyelt identitásokkal nem kell regisztrálnia az egyszerű szolgáltatásokat az Azure AD-ben. Vagy őrizze meg a hitelesítő adatokat, például a tanúsítványokat.

A felügyelt identitások az Azure HDInsight használhatók az Azure AD tartományi szolgáltatások eléréséhez, illetve a Azure Data Lake Storage Gen2 szükség esetén a fájlok eléréséhez.

A felügyelt identitások két típusa létezik: felhasználó által hozzárendelt és rendszerhez rendelt. Az Azure HDInsight csak a felhasználó által hozzárendelt felügyelt identitásokat támogatja. A HDInsight nem támogatja a rendszerhez rendelt felügyelt identitásokat. A felhasználó által hozzárendelt felügyelt identitás önálló Azure-erőforrásként jön létre, amelyet aztán hozzárendelhet egy vagy több Azure-szolgáltatási példányhoz. Ezzel szemben egy rendszerhez rendelt felügyelt identitás jön létre az Azure AD-ben, majd közvetlenül egy adott Azure-szolgáltatási példányon automatikusan engedélyezve lesz. A rendszer által hozzárendelt felügyelt identitás élettartama ezután a szolgáltatás azon példányának élettartamához kötődik, amelyen engedélyezve van.

## <a name="hdinsight-managed-identity-implementation"></a>HDInsight felügyelt identitás implementálása

Az Azure HDInsight a felügyelt identitások csak a belső összetevőkhöz használhatók a HDInsight szolgáltatásban. A külső szolgáltatások eléréséhez a HDInsight telepített felügyelt identitások használatával jelenleg nincs támogatott módszer a hozzáférési tokenek létrehozásához. Bizonyos Azure-szolgáltatások, például a számítási virtuális gépek esetében a felügyelt identitások olyan végponttal valósulnak meg, amelyet hozzáférési jogkivonatok beszerzésére használhat. Ez a végpont jelenleg nem érhető el a HDInsight-csomópontokban.

Ha el kell érnie az alkalmazásait, hogy elkerülje a titkokat/jelszavakat az elemzési feladatok (például a SCALA-feladatok) esetében, distrubte saját tanúsítványait a fürtcsomópontok számára a parancsfájlok használatával, majd a tanúsítvány használatával megszerezheti a hozzáférési jogkivonatot (például az Azure kulcstartó eléréséhez).

## <a name="create-a-managed-identity"></a>Felügyelt identitás létrehozása

A felügyelt identitások a következő módszerek bármelyikével hozhatók létre:

* [Azure Portalra](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* [Azure Resource Manager](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)
* [Azure CLI](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)

A felügyelt identitás konfigurálásának hátralévő lépései attól függnek, hogy milyen helyzetben lesz használatban.

## <a name="managed-identity-scenarios-in-azure-hdinsight"></a>Felügyelt identitási forgatókönyvek az Azure HDInsight

A felügyelt identitásokat több forgatókönyvben használják az Azure HDInsight. A részletes beállítási és konfigurációs utasításokért tekintse meg a kapcsolódó dokumentumokat:

* [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md#create-a-user-assigned-managed-identity)
* [Enterprise Security Package](domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-and-authorize-a-managed-identity)
* [Ügyfél által felügyelt kulcson alapuló lemeztitkosítás](disk-encryption.md)

## <a name="faq"></a>GYIK

### <a name="what-happens-if-i-delete-the-managed-identity-after-the-cluster-creation"></a>Mi történik, ha törölem a felügyelt identitást a fürt létrehozása után?

A fürt problémákba ütközik, amikor a felügyelt identitásra van szükség. A fürt létrehozása után jelenleg nincs lehetőség felügyelt identitás frissítésére vagy módosítására. Ezért javasoljuk, hogy a fürt futtatókörnyezetében ne törölje a felügyelt identitást. Vagy újra létrehozhatja a fürtöt, és hozzárendelhet egy új felügyelt identitást.

## <a name="next-steps"></a>További lépések

* [Mit kell tudni az Azure-erőforrások felügyelt identitásairól?](../active-directory/managed-identities-azure-resources/overview.md)
