---
title: Tárfiókok konfigurálása az Azure-ban cloudyn |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan konfigurálja az Azure storage-fiókok és az AWS-tároló gyűjtők a Cloudyn.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/20/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: benshy
ms.custom: secdec18
ms.openlocfilehash: 1ac4442aa5a7e5e4367a03d33169412d37b3f1ea
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229931"
---
# <a name="configure-storage-accounts-for-cloudyn"></a>Storage-fiókok konfigurálása a Cloudynben

<!--- intent: As a Cloudyn user, I want to configure Cloudyn to use my cloud service provider storage account to store my reports. -->

A Cloudyn portál, az Azure storage vagy az AWS-tároló gyűjtők mentheti a Cloudyn jelentésekben. A Cloudyn portálon a jelentések mentése az ingyenesen elérhető. Azonban a felhőszolgáltató tárolási mentése folyamatban van a jelentések nem kötelező, és további költséget áll. Ez a cikk segít az Azure storage-fiókok és az Amazon Web Services (AWS) storage gyűjtők a jelentések tárolására konfigurálása.

## <a name="prerequisites"></a>Előfeltételek

Rendelkeznie kell egy Azure storage-fiókot vagy egy Amazon tárolási gyűjtőbe.

Ha nem rendelkezik Azure storage-fiókkal, létre kell hoznia egyet. További információ az Azure Storage-fiókok létrehozásáról: [Storage-fiók létrehozása](../storage/common/storage-quickstart-create-account.md).

Ha nem rendelkezik egy AWS simple storage service (S3) gyűjtőbe, akkor meg kell hoznia egyet. Az S3 gyűjtő létrehozásával kapcsolatos további információkért lásd: [create a Bucket](https://docs.aws.amazon.com/AmazonS3/latest/gsg/CreatingABucket.html).

## <a name="configure-your-azure-storage-account"></a>Az Azure storage-fiók konfigurálása

Konfigurálása, hogy a Cloudyn által használható az Azure storage nagyon egyszerű. Gyűjtse össze a tárfiók részleteit, és másolja őket a Cloudyn portálon.

1. Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.
2. Kattintson a **minden szolgáltatás**elemre, válassza a **Storage-fiókok**lehetőséget, görgessen a használni kívánt Storage-fiókhoz, majd válassza ki a fiókot.
3. A Storage-fiók lap **Beállítások**területén kattintson a **hozzáférési kulcsok**elemre.
4. Másolja a **Storage-fiók nevét** és a **kapcsolatok karakterláncát** a key1 területen.  
   ![másolja a Storage-fiók nevét és a kapcsolatok karakterláncát](./media/storage-accounts/azure-storage-access-keys.png)  
5. Nyissa meg a Cloudyn portált az Azure Portalról, vagy lépjen a https://azure.cloudyn.com webhelyre, és jelentkezzen be.
6. Kattintson a fogaskerék szimbólumra, majd válassza a **jelentések tároló kezelése**lehetőséget.
7. Kattintson az **új hozzáadása +** elemre, és győződjön meg arról, hogy Microsoft Azure van kiválasztva. Illessze be az Azure Storage-fiók nevét a **név** területre. Illessze be a kapcsolódó területre a **kapcsolatok karakterláncát** . Adja meg a tároló nevét, majd kattintson a **Mentés**gombra.  
   ![illessze be az Azure Storage-fiók nevét és a kapcsolatok karakterláncát az új jelentés-tároló hozzáadása mezőbe](./media/storage-accounts/azure-cloudyn-storage.png)

   Az új jelentés az Azure storage bejegyzés jelenik meg a storage-fiók listában.  
    ![Új jelentés az Azure storage bejegyzést listában](./media/storage-accounts/azure-storage-entry.png)


Jelentések az Azure-tárolóba mentheti. A jelentésekben kattintson a **műveletek** , majd az **ütemezett jelentés**elemre. Jelentés elnevezése majd saját URL-címet, vagy használja az automatikusan létrehozott URL-címet. Válassza a **Mentés a tárolóba** lehetőséget, majd válassza ki a Storage-fiókot. Adja meg, amely lekérdezi a jelentés fájlnév fűzött előtagot. Válassza ki a CSV vagy JSON-fájl formátuma, és mentse a jelentést.

## <a name="configure-an-aws-storage-bucket"></a>Az AWS tárolási gyűjtőbe konfigurálása

A Cloudyn meglévő AWS hitelesítő adatait használja: felhasználó vagy szerepkör, a jelentések mentése a gyűjtőhöz. A hozzáférés teszteléséhez a Cloudyn egy kisméretű szövegfájlt próbál menteni a gyűjtőbe a _Check-Bucket-Permission. txt_fájl nevével.

Azt adja meg a Cloudyn-szerepkör vagy felhasználó PutObject engedéllyel rendelkező a gyűjtőhöz. Ezután használja egy meglévő gyűjtőbe, vagy hozzon létre egy új jelentés mentéséhez. Végül döntse el, a tárolási osztály kezelése, életciklus-szabályokat állíthat be, vagy távolítsa el a felesleges fájlokat.

###  <a name="assign-permissions-to-your-aws-user-or-role"></a>Engedélyek hozzárendelése az AWS-felhasználó vagy szerepkör

Amikor létrehoz egy új szabályzatot, meg kell adnia egy S3 gyűjtőt mentéséhez szükséges pontos engedélyekkel.

1. Jelentkezzen be az AWS-konzolra, és válassza a **szolgáltatások**lehetőséget.
2. Válassza a **iam** lehetőséget a szolgáltatások listájából.
3. A konzol bal oldalán válassza a **házirendek** elemet, majd kattintson a **házirend létrehozása**elemre.
4. Kattintson a **JSON** fülre.
5. A következő szabályzatot lehetővé teszi, hogy egy S3 gyűjtőt mentéséhez. Másolja és illessze be a következő házirend-példát a **JSON** -lapra. cserélje le &lt;bucketname&gt; a gyűjtő nevére.

   ```json
   {
    "Version": "2012-10-17",
    "Statement": [
      {
        "Sid":  "CloudynSaveReport2S3",
        "Effect":      "Allow",
        "Action": [
          "s3:PutObject"
        ],
        "Resource": [
          "arn:aws:s3:::<bucketname>/*"
        ]
      }
    ]
   }
   ```

6. Kattintson a **házirend áttekintése**elemre.  
    ![AWS JSON-szabályzat, amely példákat mutat be](./media/storage-accounts/aws-policy.png)  
7. A szabályzat áttekintése lapon írja be a házirend nevét. Például: _CloudynSaveReport2S3_.
8. Kattintson a **házirend létrehozása**elemre.

### <a name="attach-the-policy-to-a-cloudyn-role-or-user-in-your-account"></a>A szabályzat csatolása a Cloudyn-szerepkör vagy felhasználó fiókjában

Csatlakoztassa az új házirend, az AWS konzolon nyissa meg, és szerkessze a Cloudyn-szerepkör vagy a felhasználó.

1. Jelentkezzen be az AWS-konzolra, és válassza a **szolgáltatások**lehetőséget, majd válassza a **iam** lehetőséget a szolgáltatások listájából.
2. Válassza ki a **szerepköröket** vagy a **felhasználókat** a konzol bal oldalán.

**Szerepkörök esetén:**

  1. Kattintson a Cloudyn-szerepkör nevét.
  2. Az **engedélyek** lapon kattintson a **házirend csatolása**elemre.
  3. Keresse meg a létrehozott szabályzatot, és válassza ki, majd kattintson a **házirend csatolása**elemre.
    ![például a Cloudyn-szerepkörhöz csatolt szabályzatot](./media/storage-accounts/aws-attach-policy-role.png)

**Felhasználók számára:**

1. Válassza ki a Cloudyn felhasználói.
2. Az **engedélyek** lapon kattintson az **engedélyek hozzáadása**lehetőségre.
3. A **jogosultság megadása** szakaszban válassza a **meglévő szabályzatok közvetlen csatolása**lehetőséget.
4. Keresse meg a létrehozott szabályzatot, és válassza ki, majd kattintson a **Tovább gombra: Áttekintés**.
5. Az engedélyek hozzáadása szerepkör neve lapon kattintson az **engedélyek hozzáadása**lehetőségre.  
    ![például a Cloudyn-felhasználóhoz csatolt szabályzatot](./media/storage-accounts/aws-attach-policy-user.png)


### <a name="optional-set-permission-with-bucket-policy"></a>Választható lehetőség: Engedély beállítása a bucket szabályzat

Jelentéseket hozhat létre, a gyűjtő házirend segítségével S3 gyűjtő számára is beállíthatja. A klasszikus S3 nézetben:

1. Létrehozhat vagy kiválaszthat egy meglévő gyűjtőbe.
2. Válassza az **engedélyek** fület, majd kattintson a **gyűjtő házirend**elemre.
3. Másolja és illessze be a következő házirend-mintát. Cserélje le &lt;Bucket\_Name&gt; és &lt;Cloudyn\_elvét&gt; a gyűjtőre. Cserélje le a szerepkör vagy a Cloudyn által használt felhasználói információ.

   ```
   {
   "Id": "Policy1485775646248",
   "Version": "2012-10-17",
   "Statement": [
    {
      "Sid": "SaveReport2S3",
      "Action": [
        "s3:PutObject"
      ],
      "Effect": "Allow",
      "Resource": "<bucket_name>/*",
      "Principal": {
        "AWS": [
          "<Cloudyn_principle>"
        ]
      }
    }
   ]
   }
   ```

4. A gyűjtő házirend-szerkesztőjében kattintson a **Mentés**gombra.

### <a name="add-aws-report-storage-to-cloudyn"></a>A Cloudyn AWS jelentés tárhely hozzáadása

1. Nyissa meg a Cloudyn portált az Azure Portalról, vagy lépjen a https://azure.cloudyn.com webhelyre, és jelentkezzen be.
2. Kattintson a fogaskerék szimbólumra, majd válassza a **jelentések tároló kezelése**lehetőséget.
3. Kattintson az **új hozzáadása +** elemre, és ellenőrizze, hogy az AWS ki van-e választva.
4. Válassza ki egy fiókot és a storage gyűjtőbe. Az AWS-tároló gyűjtőbe neve automatikusan kitöltve jelennek.  
    ![Példa-információk hozzáadása egy új jelentés storage használata](./media/storage-accounts/aws-cloudyn-storage.png)  
5. Kattintson a **Mentés** , majd **az OK**gombra.

    Az új AWS jelentés tárolási bejegyzés jelenik meg a storage-fiókok listájának.  
    ![Új AWS jelentés tárolási bejegyzés megjelenítése a storage-fiók listája](./media/storage-accounts/aws-storage-entry.png)


Jelentések az Azure-tárolóba mentheti. A jelentésekben kattintson a **műveletek** , majd az **ütemezett jelentés**elemre. Jelentés elnevezése majd saját URL-címet, vagy használja az automatikusan létrehozott URL-címet. Válassza a **Mentés a tárolóba** lehetőséget, majd válassza ki a Storage-fiókot. Adja meg, amely lekérdezi a jelentés fájlnév fűzött előtagot. Válassza ki a CSV vagy JSON-fájl formátuma, és mentse a jelentést.

## <a name="next-steps"></a>További lépések

- Tekintse át a [Cloudyn-jelentések ismertetése](understanding-cost-reports.md) című témakört, amely az Cloudyn-jelentések alapvető szerkezetét és funkcióit ismerteti.
