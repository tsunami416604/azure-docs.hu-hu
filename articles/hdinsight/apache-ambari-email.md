---
title: 'Oktatóanyag: Az Apache Ambari e-mail értesítéseinek konfigurálása az Azure HDInsightban'
description: Ez a cikk azt ismerteti, hogyan használhatja a SendGridet az Apache Ambari-val az e-mailes értesítésekhez.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: tutorial
ms.date: 03/10/2020
ms.openlocfilehash: 21376eb40fb40abe67f7e03d15aabd7d89ea62f8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "80082313"
---
# <a name="tutorial-configure-apache-ambari-email-notifications-in-azure-hdinsight"></a>Oktatóanyag: Az Apache Ambari e-mail értesítéseinek konfigurálása az Azure HDInsightban

Ebben az oktatóanyagban konfigurálhatja az Apache Ambari e-mail értesítéseket a SendGrid használatával. [Az Apache Ambari](./hdinsight-hadoop-manage-ambari.md) leegyszerűsíti a HDInsight-fürtök felügyeletét és figyelését azáltal, hogy egy könnyen használható webes felhasználói felületet és REST API-t biztosít. Az Ambari a HDInsight-fürtökben található, és a fürt figyelésére és a konfiguráció módosítására szolgál. [A SendGrid](https://sendgrid.com/solutions/) egy ingyenes felhőalapú e-mail szolgáltatás, amely megbízható tranzakciós e-mail kézbesítést, méretezhetőséget és valós idejű elemzést, valamint rugalmas API-kat biztosít, amelyek megkönnyítik az egyéni integrációt. Az Azure-ügyfelek havonta 25 000 ingyenes e-mailt oldhatnak fel.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Sendgrid-felhasználónév beszerzése
> * Apache Ambari e-mail értesítések konfigurálása

## <a name="prerequisites"></a>Előfeltételek

* SendGrid e-mail fiók. További [útmutatásért olvassa el az E-mailek küldése a SendGrid használatával az Azure-ral.](https://docs.microsoft.com/azure/sendgrid-dotnet-how-to-send-email)

* Egy HDInsight-fürt. Lásd: [Apache Hadoop-fürtök létrehozása az Azure Portalon.](./hdinsight-hadoop-create-linux-clusters-portal.md)

## <a name="obtain-sendgrid-username"></a>SendGrid-felhasználónév beszerzése

1. Az [Azure Portalon](https://portal.azure.com)keresse meg a SendGrid-erőforrást.

1. Az Áttekintés lapon válassza a **Kezelés**lehetőséget a fiók SendGrid weblapjára.

    ![SendGrid – áttekintés az azure portalon](./media/apache-ambari-email/azure-portal-sendgrid-manage.png)

1. A bal oldali menüben keresse meg a fiók nevét, majd **a Fiók adatai parancsra.**

    ![SendGrid irányítópult-navigáció](./media/apache-ambari-email/sendgrid-dashboard-navigation.png)

1. A **Fiók adatai** lapon rögzítse a **Felhasználónevet**.

    ![SendGrid-fiók adatai](./media/apache-ambari-email/sendgrid-account-details.png)

## <a name="configure-ambari-e-mail-notification"></a>Ambari e-mail értesítés konfigurálása

1. Egy webböngészőből keresse `https://CLUSTERNAME.azurehdinsight.net/#/main/alerts`meg `CLUSTERNAME` a , ahol a fürt neve.

1. A **Műveletek** legördülő listában válassza az **Értesítések kezelése lehetőséget.**

1. A **Riasztási értesítések kezelése ablakban** kattintson az **+** ikonra.

    ![Ambari riasztási értesítés létrehozása](./media/apache-ambari-email/azure-portal-create-notification.png)

1. A **Riasztási értesítés létrehozása** párbeszédpanelen adja meg a következő információkat:

    |Tulajdonság |Leírás |
    |---|---|
    |Név|Adja meg az értesítés nevét.|
    |Csoportok|Konfigurálja a kívánt módon.|
    |Severity|Konfigurálja a kívánt módon.|
    |Leírás|Választható.|
    |Módszer|Hagyjuk az **e-mail**.|
    |E-mail címe:|Adjon meg e-mail(eke)t az értesítések fogadásához, vesszővel elválasztva.|
    |SMTP-kiszolgáló|`smtp.sendgrid.net`|
    |SMTP-port|25 vagy 587 (titkosítatlan/TLS kapcsolatok esetén).|
    |E-mail-cím|Adja meg az e-mail címet. A címnek nem kell hitelesnek lennie.|
    |Hitelesítés használata|Jelölje be ezt a jelölőnégyzetet.|
    |Felhasználónév|Adja meg a SendGrid felhasználónevet.|
    |Jelszó|Adja meg a SendGrid erőforrás azure-beli létrehozásakor használt jelszót.|
    |Jelszó megerősítése|Írja be újra a jelszót.|
    |A TLS indítása|Jelölje be ezt a jelölőnégyzetet.|

    ![Ambari riasztási értesítés létrehozása](./media/apache-ambari-email/ambari-create-alert-notification.png)

    Kattintson a **Mentés** gombra. Vissza fog térni a **Riasztási értesítések kezelése** ablakba.

1. A **Riasztási értesítések kezelése** ablakban válassza a **Bezárás**gombot.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan konfigurálhatja az Apache Ambari e-mail értesítéseket a SendGrid használatával. Az Apache Ambari-ról az alábbiakban olvashat bővebben:

* [HDInsight-fürtök kezelése az Apache Ambari webes felületével](./hdinsight-hadoop-manage-ambari.md)

* [Riasztási értesítés létrehozása](https://docs.cloudera.com/HDPDocuments/Ambari-latest/managing-and-monitoring-ambari/content/amb_create_an_alert_notification.html)
