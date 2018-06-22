## <a name="deploy-template-from-cloud-shell"></a>Sablon üzembe helyezése a Cloud Shellből

A sablon üzembe helyezéséhez a [Cloud Shell](../articles/cloud-shell/overview.md) is használható. Azonban Ön először be kell tölteni a sablon azokat a tárfiók a felhő rendszerhéj. Ha még nem használta a Cloud Shellt, a telepítésével kapcsolatban lásd [Az Azure Cloud Shell áttekintése](../articles/cloud-shell/overview.md) című cikket.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Válassza ki a Cloud Shell-erőforráscsoportot. A névminta a következő: `cloud-shell-storage-<region>`.

   ![Erőforráscsoport kiválasztása](./media/resource-manager-cloud-shell-deploy/select-cs-resource-group.png)

1. Válassza ki a Cloud Shell tárfiókját.

   ![Adattároló fiók kiválasztása](./media/resource-manager-cloud-shell-deploy/select-storage.png)

1. Válassza ki **Blobok**.

   ![Válassza ki a blobok](./media/resource-manager-cloud-shell-deploy/select-blobs.png)

1. Válassza ki **+ tároló**.

   ![Tároló hozzáadása](./media/resource-manager-cloud-shell-deploy/add-container.png)

1. A tároló adja meg egy nevet és olyan hozzáférési szint. Ez a cikk a minta-sablon nem bizalmas információkat tartalmaznak, ezt lehetővé teszik a névtelen olvasási hozzáférés. Kattintson az **OK** gombra.

   ![Adja meg tároló](./media/resource-manager-cloud-shell-deploy/provide-container-values.png)

1. Válassza ki a létrehozott tároló.

   ![Válassza ki az új tároló](./media/resource-manager-cloud-shell-deploy/select-container.png)

1. Válassza a **Feltöltés** lehetőséget.

   ![Blob feltöltése](./media/resource-manager-cloud-shell-deploy/upload-blob.png)

1. Keresse meg és töltse fel a sablont.

   ![Fájl feltöltése](./media/resource-manager-cloud-shell-deploy/find-and-upload-template.png)

1. Miután feltöltötte, válassza ki a sablont.

   ![Válassza ki az új sablon](./media/resource-manager-cloud-shell-deploy/select-new-template.png)

1. Másolja az URL-címet.

   ![URL-Címének másolása](./media/resource-manager-cloud-shell-deploy/copy-url.png)

1. Nyissa meg a parancssort.

   ![Cloud Shell megnyitása](./media/resource-manager-cloud-shell-deploy/start-cloud-shell.png)
