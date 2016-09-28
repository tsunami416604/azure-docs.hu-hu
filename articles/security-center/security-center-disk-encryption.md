<properties
   pageTitle="Azure virtuális gép titkosítása | Microsoft Azure"
   description="A dokumentum segítséget nyújt Önnek virtuális gépeinek az Azure Security Center által küldött riasztást követő titkosításában."
   services="security, security-center"
   documentationCenter="na"
   authors="TomShinder"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/27/2016"
   ms.author="tomsh"/>


# Azure virtuális gép titkosítása
Az Azure Security Center riasztást küld Önnek, ha azt észleli, hogy egyes virtuális gépek nincsenek titkosítva. Ezek a riasztások magas súlyossági szinttel jelennek meg. A rendszer ilyenkor javasolja, hogy titkosítsa az érintett virtuális gépeket.

![Lemeztitkosításra vonatkozó javaslat](./media/security-center-disk-encryption\security-center-disk-encryption-fig1.png)

> [AZURE.NOTE] A jelen dokumentumban szereplő információk az Azure Security Center előzetes verziójára vonatkoznak.

Az Azure Security Center által azonosított Azure virtuális gépek titkosításához a következő lépéseket javasoljuk:

- Az Azure PowerShell telepítése és konfigurálása. Ez lehetővé teszi az Azure virtuális gépek titkosításához szükséges előfeltételek beállításához elengedhetetlen PowerShell-parancsok futtatását.
- Az Azure Disk Encryption előfeltétel-konfigurációs Azure PowerShell -parancsprogram beszerzése és futtatása
- A virtuális gépek titkosítása

A dokumentum célja, hogy Ön akkor is el tudja végezni a virtuális gépek titkosítását, ha csak alapszintű ismeretekkel rendelkezik az Azure PowerShell terén (vagy akár egyáltalában nem ismeri a programot).
A lépéseknél azt vettük alapul, hogy az Azure Disk Encryption titkosítás beállítására használt ügyfélszámítógépen Windows 10 fut.

Az előfeltételek beállítására, valamint az Azure virtuális gépek titkosítására számos különböző módszer létezik. Ha Ön jól ismeri az Azure PowerShell vagy az Azure parancssori felületét, érdemes lehet más megközelítést választani.

> [AZURE.NOTE] Ha további információkra kíváncsi az Azure virtuális gépek beállításának alternatív módszereiről, olvassa el a következő cikket: [Azure Disk Encryption for Windows and Linux Azure Virtual Machines](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0) (Azure Disk Encryption windowsos és linuxos Azure virtuális gépekhez).

## Az Azure PowerShell telepítése és konfigurálása
Először telepítenie kell a számítógépre az Azure PowerShell 1.2.1-es vagy újabb verzióját. A [How to install and configure Azure PowerShell](../powershell-install-configure.md) (Az Azure PowerShell telepítése és konfigurálása) című cikkben minden lépést megtalál, amelyet el kell végeznie az Azure PowerShell telepítéséhez. A legegyszerűbb, ha a cikkben említett Web PI telepítési módszert használja. Ha a gépen már fut az Azure PowerShell, akkor is telepítse újra a Web PI módszerrel, hogy biztosan a legújabb verziót használja.


## Az Azure Disk Encryption titkosítási előfeltétel-konfigurációs parancsprogram beszerzése és futtatása
Az Azure Disk Encryption előfeltétel-konfigurációs parancsprogram beállítja az Azure virtuális gépek titkosításához szükséges összes előfeltételt.

1.  Nyissa meg a GitHubon az [Azure Disk Encryption előfeltétel-beállító parancsprogramot](https://github.com/Azure/azure-powershell/blob/dev/src/ResourceManager/Compute/Commands.Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1) tartalmazó lapot.
2.  A GitHub-lapon kattintson a **Raw** (Nyers) gombra.
3.  A lapon található összes szöveg kijelöléséhez nyomja le a **CTRL-A** billentyűkombinációt, majd a kijelölt szöveg vágólapra másolásához nyomja le a **CTRL-C** kombinációt.
4.  Nyissa meg a **Jegyzettömböt**, és másolja a vágólapra helyezett szöveget egy üres dokumentumba.
5.  Hozzon létre egy új mappát a C: meghajtón **AzureADEScript** néven.
6.  Mentse a Jegyzettömbben létrehozott fájlt – Kattintson a **Fájl** menüre, majd a **Mentés másként** elemre. A Fájlnév szövegmezőben adja meg a következőt: **”ADEPrereqScript.ps1”**, majd kattintson a **Mentés** gombra. (Ne felejtse el idézőjelbe tenni a nevet, ellenkező esetben a rendszer .txt-fájlként fogja menteni a fájlt).

Most, hogy mentette a parancsprogram tartalmát, nyissa meg azt a PowerShell ISE-ben:

1.  Kattintson a Start menü **Cortana** elemére. Kérje meg **Cortanát** a „PowerShell” megkeresésére: ehhez írja be a **PowerShell** szót Cortana keresőmezőjébe.
2.  Kattintson jobb gombbal a **Windows PowerShell ISE** elemre, majd válassza a **Futtatás rendszergazdaként** lehetőséget.
3.  Az **Administrator: Windows PowerShell ISE** ablakban kattintson a **View** (Megtekintés) elemre, majd a **Show Script Pane** (Parancsprogramablak megjelenítése) lehetőségre.
4.  Ha az ablak jobb oldalán megjelenik a **Commands** (Parancsok) panel, kattintson a jobb felső sarokban található **„x”** gombra a bezárásához. Ha a szöveg túl kicsi, ezért nehezen olvasható, használja a **CTRL+Plusz** billentyűkombinációt (a „Plusz” a „+” jel). Ha a szöveg túl nagy, használja a **CTRL+Mínusz** billentyűkombinációt (a „Mínusz” a „-” jel).
5.  Kattintson a **File** (Fájl) menüre, majd az **Open** (Megnyitás) elemre. Keresse meg a **C:\AzureADEScript** mappát, és kattintson duplán az **ADEPrereqScript** fájlra.
6.  Az **ADEPrereqScript** tartalmának meg kell jelennie a PowerShell ISE-ben. Itt a színkódolás segíti a különböző komponensek (például parancsok, paraméterek és változók) felismerését.

Az alábbi képhez hasonlóan kell megjelennie a programnak.

![A PowerShell ISE ablaka](./media/security-center-disk-encryption\security-center-disk-encryption-fig2.png)

A felső panel a „parancsprogrampanel”, az alsó a „konzol”. A cikk későbbi részeiben mi is használni fogjuk ezeket az elnevezéseket.

## Az Azure Disk Encryption előfeltétel-konfigurációs PowerShell-parancs futtatása

Az Azure Disk Encryption előfeltétel-konfigurációs parancsprogram a következő információkat kéri az elindítását követően:

- **Resource Group Name** (Erőforráscsoport neve) – Annak az erőforráscsoportnak a neve, amelyben el szeretné helyezni a kulcstárolót.  Ha a megadott néven még nem található erőforráscsoport, a rendszer létrehoz egyet a beírt néven. Ha már van az előfizetésben olyan erőforráscsoport, amelyet erre a célra kíván használni, adja meg ennek az erőforráscsoportnak a nevét.
- **Key Vault Name** (Kulcstároló neve) – Annak a kulcstárolónak a neve, amelyben el kívánja helyezni a titkosítási kulcsokat. Ha a megadott néven még nem található kulcstároló, a rendszer létrehoz egyet a beírt néven. Ha már létezik a kulcstároló, amelyet erre a célra kíván használni, adja meg ennek a kulcstárolónak a nevét.
- **Location** (Hely) – A kulcstároló helye. A kulcstárolónak, valamint a titkosítani kívánt virtuális gépeknek ugyanazon a helyen kell lenniük. Ha nem ismeri a helyet, a cikk későbbi részében bemutatjuk, hogyan tudhatja meg.
- **Azure Active Directory Application Name** (Azure Active Directory-alkalmazás neve) – Az Azure Active Directory-alkalmazás neve, amelynek segítségével a rendszer beírja a titkokat a kulcstárolóba. Ha a megadott néven még nem létezik alkalmazás, a rendszer létrehoz egyet a beírt néven. Ha már létezik az Azure Active Directory-alkalmazás, amelyet erre a célra kíván használni, adja meg ennek az Azure Active Directory-alkalmazásnak a nevét.

> [AZURE.NOTE] Ha szeretné tudni, miért van szükség Azure Active Directory-alkalmazás létrehozására, olvassa el a [Getting Started with Azure Key Vault](../key-vault/key-vault-get-started.md) (Az Azure Key Vault megismerése) című cikk *Register an application with Azure Active Directory* (Alkalmazások regisztrálása az Azure Active Directoryban) című fejezetét.

Az Azure virtuális gép titkosításához végezze el az alábbi lépéseket:

1.  Ha korábban bezárta a PowerShell ISE-t, indítsa el a PowerShell ISE-t magas jogosultsági szinten. Ha a PowerShell ISE még nincs megnyitva, kövesse a cikk korábbi szakaszaiban ismertetett utasításokat. Ha korábban bezárta a parancsprogramot, nyissa meg az **ADEPrereqScript.ps1** fájlt. Ehhez kattintson a **Fájl** menüre, majd a **Megnyitás** gombra, és válassza ki a parancsprogramot a **c:\AzureADEScript** könyvtárból. Ha a cikkben eddig szereplő összes utasítást elvégezte, egyszerűen folytassa a következő lépéssel.
2.  A PowerShell ISE konzoljában (azaz az alsó panelen) váltsa át a fókuszt a parancsprogram helyére. Ehhez írja be: **cd c:\AzureADEScript**, majd nyomja le az **ENTER** billentyűt.
3.  Állítsa át a gépén úgy a végrehajtási szabályzatot, hogy le tudja futtatni a parancsprogramot. Írja be a konzolba a **Set-ExecutionPolicy Unrestricted** parancsot, majd nyomja le az ENTER billentyűt. Ha megjelenik a végrehajtási szabályzat módosításának hatásaira figyelmeztető párbeszédpanel, kattintson a **Yes to all** (Igen mindegyikre) vagy a **Yes** (Igen) lehetőségre (Ha megjelenik a **Yes to all** lehetőség, válassza ezt, ha nem jelenik meg a **Yes to all** lehetőség, válassza egyszerűen a **Yes** elemet).
4.  Jelentkezzen be Azure-fiókjába. Írja be a konzolba a **Login-AzureRmAccount** parancsot, majd nyomja le az **ENTER** billentyűt. Megjelenik egy párbeszédpanel, ahol megadhatja bejelentkezési adatait. (A virtuális gépeket csak akkor tudja titkosítani, ha rendelkezik a módosításukhoz szükséges jogokkal. Ha nem biztos a dolgában, forduljon az előfizetés tulajdonosához vagy a rendszergazdához). Megjelennek a következő információk: **Environment**, **Account**, **TenantId**, **SubscriptionId** és **CurrentStorageAccount**. Másolja a Jegyzettömbbe a **SubscriptionId** tartalmát. Erre a 6. lépésnél lesz szükség.
5.  Tudja meg, hogy milyen előfizetéshez tartozik a virtuális gép, illetve tudja meg a virtuális gép helyét. Lépjen a [https://portal.azure.com](ttps://portal.azure.com) lapra, és jelentkezzen be.  Kattintson a lap bal oldalán található **Virtuális gépek** elemre. A listában megtekintheti a virtuális gépeit, és hogy milyen előfizetéshez tartoznak.

    ![Virtuális gépek](./media/security-center-disk-encryption\security-center-disk-encryption-fig3.png)

6.  Térjen vissza a PowerShell ISE-hez. Állítsa be az előfizetési kontextust, amelyben a parancsprogram futni fog. Írja be a konzolba a **Select-AzureRmSubscription –SubscriptionId <Ön_előfizetés-azonosítója>** parancsot (az **<Ön_előfizetés-azonosítója >** helyére írja be tényleges előfizetés-azonosítóját), majd nyomja le az **ENTER** billentyűt. Megjelennek a következő információk: Environment, **Account**, **TenantId**, **SubscriptionId** és **CurrentStorageAccount**.
7.  Most készen áll a parancsprogram futtatására. Kattintson a **Run Script** (Parancsprogram futtatása) gombra, vagy nyomja le a billentyűzet **F5** billentyűjét.

    ![A PowerShell-parancsprogram futtatása](./media/security-center-disk-encryption\security-center-disk-encryption-fig4.png)

8.  A parancsprogram kéri a **resourceGroupName:** megadását. Adja meg a használni kívánt *erőforráscsoport* nevét, majd nyomja le az **ENTER** billentyűt. Ha még nem rendelkezik ilyennel, írja be az újonnan létrehozandó elem nevét. Ha már létezik az *erőforráscsoport*, amelyet használni kíván (például az, amelyhez a virtuális gép is tartozik), adja meg ennek a meglévő erőforráscsoportnak a nevét.
9.  A parancsprogram kéri a **keyVaultName:** megadását. Adja meg a használni kívánt *kulcstároló* nevét, majd nyomja meg az ENTER billentyűt. Ha még nem rendelkezik ilyennel, írja be az újonnan létrehozandó elem nevét. Ha már létezik a kulcstároló, amelyet erre a célra kíván használni, adja meg ennek a *kulcstárolónak* a nevét.
10. A parancsprogram kéri a **location:** megadását. Adja meg annak a helynek a nevét, ahol a titkosítani kívánt virtuális gép található, majd nyomja le az **ENTER** billentyűt. Ha nem emlékszik a helyre, végezze el ismét az 5. lépést.
11. A parancsprogram kéri az **aadAppName:** megadását. Adja meg a használni kívánt *Azure Active Directory*-alkalmazás nevét, majd nyomja le az **ENTER** billentyűt. Ha még nem rendelkezik ilyennel, írja be az újonnan létrehozandó elem nevét. Ha már létezik az *Azure Active Directory-alkalmazás*, amelyet erre a célra kíván használni, adja meg a meglévő *Azure Active Directory-alkalmazásnak* a nevét.
12. Megjelenik egy párbeszédpanel. Adja meg a bejelentkezési adatait (igen, egyszer már bejelentkezett, de erre most ismét szükség van).
13. Lefut a parancsprogram. Ha a futtatás befejeződött, a parancsprogram felszólítja az **aadClientID**, az **aadClientSecret**, a **diskEncryptionKeyVaultUrl** és a **keyVaultResourceId** értékeinek másolására. Másolja a vágólapra, majd illessze be a Jegyzettömbbe ezeket az értékeket.
14. Térjen vissza a PowerShell ISE-be, vigye a kurzort az utolsó sor végére, majd nyomja le az **ENTER** billentyűt.

A parancsprogram eredményének a következőhöz hasonlóan kell kinéznie:

![A PowerShell eredménye](./media/security-center-disk-encryption\security-center-disk-encryption-fig5.png)

## Az Azure virtuális gép titkosítása

Most már készen áll a virtuális gép titkosítására. Ha a virtuális gép ugyanahhoz az erőforráscsoporthoz tartozik, mint a kulcstároló, továbbléphet a titkosításhoz szükséges lépésekre. Ha azonban a virtuális gép nem ugyanahhoz az erőforráscsoporthoz tartozik, mint a kulcstároló, írja be a következő parancsot a PowerShell ISE konzoljába:

**$resourceGroupName = <’Virtuális_gép_erőforráscsoportja’>**

A **<Virtuális_gép_erőforráscsoportja>** helyére szimpla idézőjelbe téve írja be a virtuális gépet tartalmazó erőforráscsoport nevét. Nyomja le az **ENTER** billentyűt.
Ha szeretné ellenőrizni, hogy a megfelelő erőforráscsoport-nevet adta-e meg, írja be a következőt a PowerShell ISE konzoljába:

**$resourceGroupName**

Nyomja le az **ENTER** billentyűt. Meg kell jelennie a virtuális gépeket tartalmazó erőforráscsoport nevének. Példa:

![A PowerShell eredménye](./media/security-center-disk-encryption\security-center-disk-encryption-fig6.png)

### A titkosítás lépései

Először közölnie kell a PowerShell-lel a titkosítani kívánt virtuális gép nevét. Írja be a konzolba a következőt:

**$vmName = <’virtuális_gép_neve’>**

A **<’virtuális_gép_neve’>** helyére írja be a virtuális gép nevét (ne feledkezzen meg a szimpla idézőjelekről), majd nyomja le az **ENTER** billentyűt.

Ha szeretné ellenőrizni, hogy a megfelelő virtuális gép nevét adta-e meg, írja be a következőt:

**$vmName**

Nyomja le az **ENTER** billentyűt. Meg kell jelennie a titkosítani kívánt virtuális gép nevének. Példa:

![A PowerShell eredménye](./media/security-center-disk-encryption\security-center-disk-encryption-fig7.png)

A virtuális gépet titkosító titkosítási parancs futtatására két módszer áll rendelkezésre. Első módszer: írja be az alábbi parancsot a PowerShell ISE konzoljába:

~~~
Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $resourceGroupName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId
~~~

A parancs beírását követően nyomja le az **ENTER** billentyűt.

Második módszer: kattintson a parancssori panelbe (azaz a PowerShell ISE felső paneljébe), majd görgessen le a parancsprogram aljára. Jelölje ki a fenti parancsot, kattintson rá a jobb gombbal, majd kattintson a **Run Selection** (Kijelölés futtatása) elemre, vagy nyomja meg a billentyűzet **F8** gombját.

![PowerShell ISE](./media/security-center-disk-encryption\security-center-disk-encryption-fig8.png)

Akármelyik módszert használja, megjelenik egy párbeszédpanel, amely tájékoztatja, hogy a művelet elvégzéséhez 10–15 perc szükséges. Kattintson a **Yes** (Igen) gombra.

A titkosítási folyamat lefolyása közben visszatérhet az Azure Portalra, és megtekintheti a virtuális gép állapotát. A lap bal oldalán kattintson a **Virtuális gépek** elemre, majd a **Virtuális gépek** panelen kattintson annak a virtuális gépnek a nevére, amelyet épp titkosít. A megjelenő panelen láthatja, hogy az **Állapot** mező értéke **Frissítés**. Ez azt mutatja, hogy a titkosítás folyamatban van.

![A virtuális gép további adatai](./media/security-center-disk-encryption\security-center-disk-encryption-fig9.png)

Térjen vissza a PowerShell ISE-hez. A parancsprogram befejezését követően az alábbi képen látható ablak jelenik meg.

![A PowerShell eredménye](./media/security-center-disk-encryption\security-center-disk-encryption-fig10.png)

A virtuális gép titkosításának ellenőrzéséhez térjen vissza az Azure Portalra, majd kattintson a lap bal oldalán található **Virtual Machines** elemre. Kattintson a korábban titkosított virtuális gép nevére. A **Beállítások** panelen kattintson a **Lemezek** elemre.

![Beállítások](./media/security-center-disk-encryption\security-center-disk-encryption-fig11.png)

A **Lemezek** panelen láthatja, hogy a **Titkosítás** mező értéke **Engedélyezve**.

![Lemeztulajdonságok](./media/security-center-disk-encryption\security-center-disk-encryption-fig12.png)


## Következő lépések

Ebből a dokumentumból megtanulta, hogyan lehet Azure virtuális gépeket titkosítani. Az Azure Security Centerrel kapcsolatos további információkért olvassa el a következőket:

- [Biztonsági állapotfigyelés az Azure Security Centerben](security-center-monitoring.md) – A cikkből megismerheti az Azure-erőforrások állapotfigyelésének módját.
- [Managing and responding to security alerts in Azure Security Center](security-center-managing-and-responding-alerts.md) (Biztonsági riasztások kezelése és reagálás a riasztásokra az Azure Security Centerben) – Ebből az írásból megtanulhatja a biztonsági riasztások kezelésének és megoldásának módját.
- [Azure Security Center: GYIK](security-center-faq.md) – Gyakori kérdések a szolgáltatás használatával kapcsolatban.
- [Azure Security blog](http://blogs.msdn.com/b/azuresecurity/) – Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.



<!--HONumber=Sep16_HO4-->


