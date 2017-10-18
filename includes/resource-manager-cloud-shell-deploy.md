## <a name="deploy-template-from-cloud-shell"></a>Sablon üzembe helyezése a Cloud Shellből

A sablon üzembe helyezéséhez a [Cloud Shell](../articles/cloud-shell/overview.md) is használható. Ehhez azonban először be kell töltenie a sablont a Cloud Shell fájlmegosztásába. Ha még nem használta a Cloud Shellt, a telepítésével kapcsolatban lásd [Az Azure Cloud Shell áttekintése](../articles/cloud-shell/overview.md) című cikket.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).

1. Válassza ki a Cloud Shell-erőforráscsoportot. A névminta a következő: `cloud-shell-storage-<region>`.

   ![Erőforráscsoport kiválasztása](./media/resource-manager-cloud-shell-deploy/select-cs-resource-group.png)

1. Válassza ki a Cloud Shell tárfiókját.

   ![Adattároló fiók kiválasztása](./media/resource-manager-cloud-shell-deploy/select-storage.png)

1. Válassza a **Files** (Fájlok) lehetőséget.

   ![Fájlok kiválasztása](./media/resource-manager-cloud-shell-deploy/select-files.png)

1. Válassza ki a Cloud Shell fájlmegosztását. A névminta a következő: `cs-<user>-<domain>-com-<uniqueGuid>`.

   ![Fájlmegosztás kiválasztása](./media/resource-manager-cloud-shell-deploy/select-file-share.png)

1. Válassza az **Add directory** (Könyvtár hozzáadása) lehetőséget.

   ![Könyvtár hozzáadása](./media/resource-manager-cloud-shell-deploy/select-add-directory.png)

1. Nevezze el a könyvtárat **templates** (sablonok) néven, és válassza az **Okay** (OK) gombot.

   ![Könyvtár elnevezése](./media/resource-manager-cloud-shell-deploy/name-templates.png)

1. Jelölje ki az új könyvtárat.

   ![Könyvtár kijelölése](./media/resource-manager-cloud-shell-deploy/select-templates.png)

1. Válassza a **Feltöltés** lehetőséget.

   ![Feltöltés kiválasztása](./media/resource-manager-cloud-shell-deploy/select-upload.png)

1. Keresse meg és töltse fel a sablont.

   ![Fájl feltöltése](./media/resource-manager-cloud-shell-deploy/upload-files.png)

1. Nyissa meg a parancssort.

   ![Cloud Shell megnyitása](./media/resource-manager-cloud-shell-deploy/start-cloud-shell.png)
