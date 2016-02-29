<properties
    pageTitle="檢視存取控制服務 (Java) 所傳回的 SAML"
    description="了解如何在裝載於 Azure 上的 Java 應用程式中，檢視存取控制服務所傳回的 SAML。"
    services="active-directory" 
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor="jimbe" />

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="10/12/2015" 
    ms.author="robmcm" />

# 如何檢視 Azure 存取控制服務傳回的 SAML

本指南說明如何檢視 Azure 存取控制服務 (ACS) 傳回給應用程式的基本安全性聲明標記語言 (SAML)。 本指南是根據 [Eclipse 搭配 Azure 存取控制服務使用的驗證 Web 使用者如何][] 主題，提供程式碼來顯示 SAML 資訊。 完成後的應用程式如下所示：

![Example SAML output][saml_output]

如需 ACS 的詳細資訊，請參閱 [後續步驟](#next_steps) 一節。

> [AZURE.NOTE]
> Azure Access Services Control Filter 是社群技術預覽。 由於是發行前軟體，Microsoft 尚未提供正式支援。

## 先決條件

若要完成本指南中的工作，完成的範例， [Eclipse 搭配 Azure 存取控制服務使用的驗證 Web 使用者如何][] 作為本教學課程的起點。

## 將 JspWriter 程式庫加入至組建路徑和部署組件

新增程式庫，其中包含 **javax.servlet.jsp.JspWriter** 組建路徑和部署組件的類別。 如果您使用 Tomcat，程式庫是 **lib**, ，這位於 Apache **lib** 資料夾。

1. 在 Eclipse 的專案總管] 中，以滑鼠右鍵按一下 **MyACSHelloWorld**, ，按一下 [ **組建路徑**, ，按一下 [ **Configure Build Path**, ，按一下 **程式庫** 索引標籤，然後按一下 **新增外部 Jar**。
2. 在 **選取 JAR** ] 對話方塊中，瀏覽至所需的 JAR，加以選取，然後按一下 [ **開啟**。
3. 使用 **Properties for MyACSHelloWorld** 對話方塊仍開啟時，按一下 **部署組件**。
4. 在 **Web 部署組件** ] 對話方塊中，按一下 [ **新增**。
5. 在 **新組件指示詞** ] 對話方塊中，按一下 [ **Java Build Path Entries** 然後按一下 [ **下一步**。
6. 選取適當的程式庫，然後按一下 **完成**。
7. 按一下 [ **確定** 關閉 **Properties for MyACSHelloWorld** ] 對話方塊。

## 修改 JSP 檔案來顯示 SAML

修改 **index.jsp** 使用下列程式碼。

    <%@ page language="java" contentType="text/html; charset=UTF-8"
        pageEncoding="UTF-8"%>
        <%@ page import="javax.xml.parsers.*"
                 import="javax.xml.transform.*"
                 import="org.w3c.dom.*"
                 import="java.io.*"
                 import="javax.xml.transform.stream.*"
                 import="javax.xml.transform.dom.*"
                 import="javax.xml.xpath.*"
                 import="javax.servlet.jsp.JspWriter" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Sample ACS Filter</title>
    </head>
    <body>
        <h3>SAML information from sample ACS program</h3>
        <%!
        void displaySAMLInfo(Node node, String parent, JspWriter out)
        {
        
            try
            {
                String nodeName;
                int nChild, i;
                
                nodeName = node.getNodeName();
                out.println("<br>");
                out.println("<u>Examining <b>" + parent + nodeName + "</b></u><br>");
                   
                   // Attributes.
                   NamedNodeMap attribsMap = node.getAttributes();
                   if (null != attribsMap)
                   {
                         for (i=0; i < attribsMap.getLength(); i++)
                         {
                                Node attrib = attribsMap.item(i);
                                out.println("Attribute: <b>" + attrib.getNodeName() + "</b>: " + attrib.getNodeValue()  + "<br>");
                         }
                   }
                   
                   // Child nodes.
                   NodeList list = node.getChildNodes();
                   if (null != list)
                   {
                          nChild = list.getLength();
                          if (nChild > 0)
                          {                    
    
                                 // If it is a text node, just print the text.
                                 if (list.item(0).getNodeName() == "#text")
                                 {
                                     out.println("Text value: <b>" + list.item(0).getTextContent() + "</b><br>");
                                 }
                                 else
                                 {
                                     // Print out the child node names.
                                     out.print("Contains " + nChild + " child node(s): ");   
                                     for (i=0; i < nChild; i++)
                                     {
                                        Node temp = list.item(i);
                                        
                                        out.print("<b>" + temp.getNodeName() + "</b>");
                                        if (i < nChild - 1)
                                        {
                                            // Separate the names.
                                            out.print(", ");
                                        }
                                        else
                                        {
                                            // Finish the sentence.
                                            out.print(".");
                                        }
                                            
                                     }
                                     out.println("<br>");
                                     
                                     // Process the child nodes.
                                     for (i=0; i < nChild; i++)
                                     {
                                        Node temp = list.item(i);
                                        displaySAMLInfo(temp, parent + nodeName + "\\", out);
                                     }
                               }
                          }
                      }
                  }
                catch (Exception e)
                {
                    System.out.println("Exception encountered.");
                    e.printStackTrace();            
                }
            }
        %>
    
        <%
        try 
        {
            String data  = (String) request.getAttribute("ACSSAML");
            
            DocumentBuilder docBuilder;
            Document doc = null;
            DocumentBuilderFactory docBuilderFactory = DocumentBuilderFactory.newInstance();
            docBuilderFactory.setIgnoringElementContentWhitespace(true);
            docBuilder = docBuilderFactory.newDocumentBuilder();
            byte[] xmlDATA = data.getBytes();
            
            ByteArrayInputStream in = new ByteArrayInputStream(xmlDATA); 
            doc = docBuilder.parse(in);
            doc.getDocumentElement().normalize();
            
            // Iterate the child nodes of the doc.
            NodeList list = doc.getChildNodes();
    
            for (int i=0; i < list.getLength(); i++)
            {
                displaySAMLInfo(list.item(i), "", out);
            }
        }
        catch (Exception e) 
        {
            out.println("Exception encountered.");
            e.printStackTrace();
        }
        
        %>
    </body>
    </html>

## 執行應用程式

1. 在電腦模擬器中執行您的應用程式或部署至 Azure，記載的步驟 [Eclipse 搭配 Azure 存取控制服務使用的驗證 Web 使用者如何][]。
2. 啟動瀏覽器並開啟您的 Web 應用程式。 登入應用程式之後，您會看到 SAML 資訊，包括身分識別提供者所提供的安全性聲明。

## 後續步驟

若要進一步探索 ACS 功能及試試其他更精緻的案例，請參閱 [存取控制服務 2.0][]。

[Prerequisites]: #pre
[Modify the JSP file to display SAML]: #modify_jsp
[Add the JspWriter library to your build path and deployment assembly]: #add_library
[Run the application]: #run_application
[Next steps]: #next_steps
[Access Control Service 2.0]: http://go.microsoft.com/fwlink/?LinkID=212360
[How to Authenticate Web Users with Azure Access Control Service Using Eclipse]: ../active-directory-java-authenticate-users-access-control-eclipse
[saml_output]: ./media/active-directory-java-view-saml-returned-by-access-control/SAML_Output.png
 
