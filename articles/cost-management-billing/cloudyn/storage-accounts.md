---
title: Tárfiókok konfigurálása a Cloudynhez az Azure-ban | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan konfigurálhat Azure-tárfiókot és AWS-tárolót a Cloudynhez.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/24/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: benshy
ms.custom: secdec18
ms.openlocfilehash: ec6c68d209f867ab3547e855f3cf754bc27d53c5
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2020
ms.locfileid: "77117623"
---
# <a name="configure-storage-accounts-for-cloudyn"></a>Tárfiókok konfigurálása a Cloudynhez

<!--- intent: As a Cloudyn user, I want to configure Cloudyn to use my cloud service provider storage account to store my reports. -->

A Cloudyn-jelentéseket mentheti a Cloudyn portálon, illetve Azure- vagy AWS-tárolókban. A jelentések mentése a Cloudyn portálon ingyenes. A jelentéseket mentheti azonban saját felhőszolgáltatói tárterületre is, ami további költséggel jár. Ebből a cikkből megtudhatja, hogyan konfigurálhat Azure-tárfiókot és Amazon Web Services- (AWS-) tárolót a jelentések tárolásához.

## <a name="prerequisites"></a>Előfeltételek

Rendelkeznie kell Azure-tárfiókkal vagy Amazon-tárolóval.

Ha nem rendelkezik Azure-tárfiókkal, létre kell hoznia egyet. Az Azure-tárfiók létrehozásával kapcsolatban a [tárfiókok létrehozását](../../storage/common/storage-account-create.md) ismertető cikkben tekinthet meg további információt.

Ha nem rendelkezik AWS Simple Storage Service- (S3-) tárolóval, létre kell hoznia egyet. Az S3-tároló létrehozásával kapcsolatban a [tárolók létrehozását](https://docs.aws.amazon.com/AmazonS3/latest/gsg/CreatingABucket.html) ismertető cikkben tekinthet meg további információt.

## <a name="configure-your-azure-storage-account"></a>Azure-tárfiók konfigurálása

Az Azure-tároló egyszerűen konfigurálható a Cloudynnel történő használatra. Gyűjtse össze a tárfiók adatait, és másolja őket a Cloudyn portálra.

1. Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.
2. Kattintson a **Minden szolgáltatás** lehetőségre, válassza a **Tárfiókok** elemet, görgessen a használni kívánt tárfiókhoz, és jelölje ki.
3. A tárfiók oldalának **Beállítások** területén kattintson a **Hozzáférési kulcsok** gombra.
4. A key1 területen másolja a **tárfiók nevét** és a **kapcsolati sztringet**.  
   ![A tárfiók nevének és a kapcsolati sztringnek a másolása](./media/storage-accounts/azure-storage-access-keys.png)  
5. Nyissa meg a Cloudyn portált az Azure Portalról, vagy lépjen a [https://azure.cloudyn.com](https://azure.cloudyn.com) webhelyre, és jelentkezzen be.
6. Kattintson a fogaskerék szimbólumra, majd válassza a **Reports Storage Management** (Jelentéstárolás kezelése) elemet.
7. Kattintson az **Add New +** (Új hozzáadása +) parancsra, és válassza ki a Microsoft Azure elemet. Illessze be az Azure-tárfiók nevét a **Name** (Név) mezőbe. Illessze be a **kapcsolati sztringet** a megfelelő mezőbe. Adja meg a tároló nevét, és kattintson a **Save** (Mentés) gombra.  
   ![Az Azure-tárfiók nevének és a kapcsolati sztringnek a beillesztése az Add a new report storage (Új jelentéstároló felvétele) lapon](./media/storage-accounts/azure-cloudyn-storage.png)

   Az új Azure-jelentéstároló bejegyzése megjelenik a tárfiók listájában.  
    ![Az új Azure-jelentéstároló bejegyzése a listában](./media/storage-accounts/azure-storage-entry.png)


Mostantól az Azure-tárolóba mentheti a jelentéseket. A jelentésekben kattintson az **Actions** (Műveletek) gombra, majd válassza a **Schedule report** (Jelentés ütemezése) lehetőséget. Nevezze el a jelentést, majd adja hozzá a saját URL-címét, vagy használja az automatikusan létrehozott URL-címet. Válassza a **Save to storage** (Mentés tárolóba) elemet, majd válassza ki a tárfiókot. Adjon meg egy előtagot, amelyet a rendszer a jelentés fájlnevéhez fűz. Válassza ki a CSV- vagy a JSON-fájlformátumot, majd mentse a jelentést.

## <a name="configure-an-aws-storage-bucket"></a>AWS-tároló konfigurálása

A Cloudyn a meglévő AWS hitelesítő adatokat, a felhasználót vagy szerepkört használja a jelentések tárolóba mentéséhez. A hozzáférés teszteléséhez a Cloudyn egy kis méretű szövegfájlt próbál menteni a tárolóba a _check-bucket-permission.txt_ fájlnévvel.

Önnek kell megadni a Cloudyn számára a tárolóhoz a PutObject engedéllyel rendelkező szerepkört vagy felhasználót. A jelentések mentéséhez ezután használhat egy már meglévő tárolót, vagy létrehozhat egy újat. Végül döntse el, hogyan kezeli a tárolóosztályt, állítja be az életciklus-szabályokat, vagy távolítja el a szükségtelen fájlokat.

###  <a name="assign-permissions-to-your-aws-user-or-role"></a>Engedélyek hozzárendelése az AWS-felhasználóhoz vagy -szerepkörhöz

Új szabályzat létrehozásakor adja meg a jelentések S3-tárolóba történő mentéséhez szükséges pontos engedélyeket.

1. Jelentkezzen be az AWS-konzolba, és válassza a **Services** (Szolgáltatások) elemet.
2. A szolgáltatások listájában válassza az **IAM** lehetőséget.
3. Válassza a konzol bal oldalán található **Policies** (Szabályzatok) elemet, majd kattintson a **Create Policy** (Szabályzat létrehozása) parancsra.
4. Kattintson a **JSON** lapra.
5. A következő szabályzat lehetővé teszi a jelentések S3-tárolóba történő mentését. Másolja és illessze be az alábbi szabályzatpéldát a **JSON** lapra. Cserélje le &lt;bucketname&gt; nevet a saját tárolójának nevére.

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

6. Kattintson a **Review policy** (Szabályzat áttekintése) gombra.  
    ![A példaadatokat megjelenítő AWS JSON-szabályzat](./media/storage-accounts/aws-policy.png)  
7. A Review policy (Szabályzat áttekintése) oldalon írja be a szabályzat nevét. Például: _CloudynSaveReport2S3_.
8. Kattintson a **Create policy** (Szabályzat létrehozása) parancsra.

### <a name="attach-the-policy-to-a-cloudyn-role-or-user-in-your-account"></a>A szabályzat csatolása Cloudyn-szerepkörhöz vagy -felhasználóhoz a fiókban

Az új szabályzat csatolásához nyissa meg az AWS-konzolt, és szerkessze a Cloudyn-szerepkört vagy -felhasználót.

1. Jelentkezzen be az AWS-konzolba és válassza a **Services** (Szolgáltatások) lehetőséget, majd a szolgáltatások listájából válassza az **IAM** elemet.
2. Válassza a konzol bal oldalán található **Roles** (Szerepkörök) vagy **Users** (Felhasználók) lehetőséget.

**Szerepkörök esetén:**

  1. Kattintson a Cloudyn-szerepkör nevére.
  2. A **Permissions** (Engedélyek) lapon kattintson az **Attach Policy** (Szabályzat csatolása) gombra.
  3. Keresse meg a létrehozott szabályzatot, jelölje ki, majd kattintson az **Attach Policy** (Szabályzat csatolása) elemre.
    ![A Cloudyn-szerepkörhöz csatolt példaszabályzat](./media/storage-accounts/aws-attach-policy-role.png)

**Felhasználók esetén:**

1. Jelölje ki a Cloudyn-felhasználót.
2. A **Permissions** (Engedélyek) lapon kattintson az **Add permissions** (Engedélyek hozzáadása) lehetőségre.
3. A **Grant Permission** (Engedély megadása) szakaszban válassza az **Attach existing policies directly** (Meglévő szabályzatok közvetlen csatolása) elemet.
4. Keresse meg a létrehozott szabályzatot, jelölje ki, majd kattintson a **Next: Review** (Következő: Áttekintés) elemre.
5. Az Add permissions to role name (Engedélyek hozzáadása szerepkörnévhez) oldalon kattintson az **Add permissions** (Engedélyek hozzáadása) elemre.  
    ![A Cloudyn-felhasználóhoz csatolt példaszabályzat](./media/storage-accounts/aws-attach-policy-user.png)


### <a name="optional-set-permission-with-bucket-policy"></a>Nem kötelező: Engedély beállítása tárolószabályzattal

A jelentéseknek az S3-tárolóban történő létrehozására vonatkozó engedélyt tárolószabályzattal is beállíthatja. A klasszikus S3-nézetben:

1. Hozzon létre egy tárolót, vagy válasszon ki egy meglévőt.
2. Kattintson a **Permissions** (Engedélyek) lapra, majd kattintson a **Bucket policy** (Tárolószabályzat) elemre.
3. Másolja és illessze be az alábbi szabályzatpéldát. Cserélje le a &lt;bucket\_name&gt; és a &lt;Cloudyn\_principle&gt; sztringet a tároló ARN-jére. Cserélje le a Cloudyn által használt szerepkör vagy felhasználó ARN-jét.

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

4. Kattintson a Bucket policy (Tárolószabályzat) szerkesztőjének **Save** (Mentés) gombjára.

### <a name="add-aws-report-storage-to-cloudyn"></a>AWS-jelentéstároló hozzáadása a Cloudynhez

1. Nyissa meg a Cloudyn portált az Azure Portalról, vagy lépjen a [https://azure.cloudyn.com](https://azure.cloudyn.com) webhelyre, és jelentkezzen be.
2. Kattintson a fogaskerék szimbólumra, majd válassza a **Reports Storage Management** (Jelentéstárolás kezelése) elemet.
3. Kattintson az **Add New +** (Új hozzáadása +) parancsra, és válassza ki az AWS elemet.
4. Válasszon ki egy fiókot és egy tárolót. Az AWS-tároló nevét a rendszer automatikusan kitölti.  
    ![Példaadatok az Add a new report storage (Új jelentéstároló felvétele) lapon](./media/storage-accounts/aws-cloudyn-storage.png)  
5. Kattintson a **Save** (Mentés), majd az **OK** gombra.

    Az új AWS-jelentéstároló bejegyzése megjelenik a tárfiók listájában.  
    ![Az új AWS-jelentéstároló bejegyzése a tárfiók listájában](./media/storage-accounts/aws-storage-entry.png)


Mostantól az Azure-tárolóba mentheti a jelentéseket. A jelentésekben kattintson az **Actions** (Műveletek) gombra, majd válassza a **Schedule report** (Jelentés ütemezése) lehetőséget. Nevezze el a jelentést, majd adja hozzá a saját URL-címét, vagy használja az automatikusan létrehozott URL-címet. Válassza a **Save to storage** (Mentés tárolóba) elemet, majd válassza ki a tárfiókot. Adjon meg egy előtagot, amelyet a rendszer a jelentés fájlnevéhez fűz. Válassza ki a CSV- vagy a JSON-fájlformátumot, majd mentse a jelentést.

## <a name="next-steps"></a>További lépések

- Tekintse át a [Cloudyn-jelentéseket ismertető](understanding-cost-reports.md) cikket, amelyben megismerkedhet a Cloudyn-jelentések alapvető szerkezetével és funkcióival.
