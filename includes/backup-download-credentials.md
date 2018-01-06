## <a name="using-vault-credentials-to-authenticate-with-the-azure-backup-service"></a>A hitelesítést az Azure Backup szolgáltatással a tárolói hitelesítő adatok használatával
Biztonsági másolatot készíthet egy helyszíni kiszolgálón (a Windows ügyfél vagy a Windows Server vagy a Data Protection Manager-kiszolgáló) az Azure-ba, mielőtt a Recovery Services-tároló kiszolgáló hitelesítésére. A tároló hitelesítési adatait tartalmazó fájlt használja a kiszolgáló Azure hitelesítésére. Az Azure PowerShell használt "közzétételi beállítások" fájl, amelynek hasonlóak a tárolói hitelesítő adatokat.

### <a name="what-is-the-vault-credential-file"></a>Mi a tároló hitelesítőadat-fájlja?
A tároló hitelesítőadat-fájlja a portál által az egyes Backup-tárolókhoz létrehozott tanúsítvány. A portál ezután feltölti a nyilvános kulcsot az Access Control Service (ACS) szolgáltatásba. A tanúsítvány titkos kulcsát a munkafolyamatot, amely meg van adva a gép regisztrációs munkafolyamat bemenetként részeként a felhasználó számára legyen elérhető. Ez hitelesíti a gép biztonsági mentési adatokat küldhet egy meghatározott tárolóban, az Azure Backup szolgáltatásban.

A tároló hitelesítőadat-fájlja csak a regisztrációs munkafolyamat során használható. A felhasználó felelőssége, hogy győződjön meg arról, hogy a tároló hitelesítési adatait tartalmazó fájlt ne legyenek veszélyben. Ha rosszindulatú felhasználó kezébe kerül, a tároló hitelesítőadat-fájljával más gépek is regisztrálhatók ugyanabban a tárolóban. Azonban mivel a biztonsági mentési adatok titkosítása a egy hozzáférési kódot, amely az ügyfél tartozik, meglévő biztonsági mentési adatok nem sérült. Ezen probléma mérséklése érdekében tárolói hitelesítő adatok beállítása az 48hrs múlva lejár. Letöltheti a tárolói hitelesítő adatokat egy biztonsági mentési tároló tetszőleges számú alkalommal – azonban csak a legújabb tárolói hitelesítő adatok fájlját alkalmazható a regisztrációs munkamenet során.

### <a name="download-the-vault-credential-file"></a>Töltse le a tárolói hitelesítő adatok fájlját
A tárolói hitelesítő adatok fájlját az Azure-portálról egy biztonságos csatornán keresztül letöltődik. Az Azure Backup szolgáltatás nem érzékeli a tanúsítványhoz tartozó titkos kulcsot, és a titkos kulcs nem őrzi meg a portál vagy a szolgáltatás. Az alábbi lépések segítségével töltse le a tárolói hitelesítő adatok fájlját a helyi számítógépen.

1. Nyissa meg a [Azure-portálon](https://ms.azure.portal.com/)
2. A bal oldali menüben válassza **minden szolgáltatás** írja be a szolgáltatások listájában **Recovery Services**. Kattintson a **Recovery Services-tárolók**.

   ![Nyissa meg a Recovery Services-tároló](../articles/backup/media/tutorial-backup-windows-server-to-azure/full-browser-open-rs-vault_2.png)
3. Kattintson a gyors üzembe helyezés lap **letöltési tárolói hitelesítő adatokat**. A portál létrehozza a tárolói hitelesítő adatok fájlját, amely letölthető.
   
   ![Letöltés](./media/backup-download-credentials/downloadvc.png)
4. A portál a tároló hitelesítő adatait, a tároló neve és az aktuális dátum kombinációja használatával hoz létre. Kattintson a **mentése** töltse le a tárolói hitelesítő adatokat a helyi fiók Letöltések mappába, vagy adjon meg egy helyet a tárolói hitelesítő adatokat a Mentés menüjéből válassza a Mentés másként.

### <a name="note"></a>Megjegyzés
* Győződjön meg arról, hogy a tárolói hitelesítő adatokat is elérhetők, a gép helyre menti. Ha a megosztás/SMB fájl tárolja, ellenőrizze, hogy a hozzáférési engedélyeket.
* A tároló hitelesítési adatait tartalmazó fájlt csak a regisztrációs munkamenet során használt.
* A tároló hitelesítési adatait tartalmazó fájlt 48hrs után lejár, és a portálról tölthető le.
* Tekintse meg az Azure Backup [gyakran ismételt kérdések](../articles/backup/backup-azure-backup-faq.md) kérdéseivel a munkafolyamatban.

