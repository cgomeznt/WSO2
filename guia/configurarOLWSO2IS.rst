Configurar OpenLDAP with WSO2 Identity Server
============================================


WSO2 Identity Server es el único servidor de identidad de código abierto que brinda soporte para diferentes tipos de almacenamiento, protocolos ligeros de acceso a directorios (LDAP) y directorios activos (AD). Para agregar OpenLDAP como user-store PRIMARIO en WSO2 IS


Como primer paso, instalar openLDAP. Coloca domain como dominio.local durante la instalación de openLDAP, esto se reutiliza como dc=dominio,dc=local 

Utilizar este link como punto de referencia para instalar OpenLDAP. https://github.com/cgomeznt/OpenLdap/blob/master/guia/instalarconfigurarmultiplemaster7.5.rst

Descargue desde la carpeta scripts los archivos ldif. https://github.com/cgomeznt/OpenLdap/blob/master/guia/scripts

Desplegar los ldif en el OpenLDAP.::

	ldapadd -Y EXTERNAL -H ldapi:// -f 97-wso2Person.ldif
	ldapadd -Y EXTERNAL -H ldapi:// -f 98-scimPerson.ldif
	ldapadd -Y EXTERNAL -H ldapi:// -f 99-identityPerson.ldif


Normalmente, el user-store PRIMARIO en WSO2 Identity Server es un LDAP incrustado. En lugar de esto, vamos usar el openLDAP instalado. Para eso abre el usuario-mgt.xml en la carpeta <carbon-home>/repository/conf/ y cambió las siguientes líneas.::

	<Property name="ConnectionURL">ldap://localhost:389</Property>
	<Property name="ConnectionName">cn=admin,dc=wso2,dc=com</Property>
	<Property name="UserSearchBase">ou=Users,dc=wso2,dc=com</Property>
	<Property name="UserEntryObjectClass">identityPerson</Property>
	<Property name="GroupSearchBase">ou=Groups,dc=wso2,dc=com</Property>
	<Property name="EmptyRolesAllowed">false</Property>

Este es el bloque completo.::

<UserStoreManager class="org.wso2.carbon.user.core.ldap.ReadWriteLDAPUserStoreManager">
            <Property name="TenantManager">org.wso2.carbon.user.core.tenant.CommonHybridLDAPTenantManager</Property>
            <Property name="ConnectionURL">ldap://localhost:389</Property>
            <Property name="ConnectionName">cn=admin,dc=wso2,dc=com</Property>
            <Property name="ConnectionPassword">root</Property>
            <Property name="AnonymousBind">false</Property>
            <Property name="UserSearchBase">ou=Users,dc=wso2,dc=com</Property>
            <Property name="UserEntryObjectClass">identityPerson</Property>
            <Property name="UserNameAttribute">uid</Property>
            <Property name="UserNameSearchFilter">(&amp;(objectClass=person)(uid=?))</Property>
            <Property name="UserNameListFilter">(objectClass=person)</Property>
            <Property name="DisplayNameAttribute"/>
            <Property name="ReadGroups">true</Property>
            <Property name="WriteGroups">true</Property>
            <Property name="GroupSearchBase">ou=Groups,dc=wso2,dc=com</Property>
            <Property name="GroupEntryObjectClass">groupOfNames</Property>
            <Property name="GroupNameAttribute">cn</Property>
            <Property name="GroupNameSearchFilter">(&amp;(objectClass=groupOfNames)(cn=?))</Property>
            <Property name="GroupNameListFilter">(objectClass=groupOfNames)</Property>
            <Property name="MembershipAttribute">member</Property>
            <Property name="BackLinksEnabled">false</Property>
            <Property name="UsernameJavaRegEx">[a-zA-Z0-9._\-|//]{3,30}$</Property>
            <Property name="UsernameJavaScriptRegEx">^[\S]{3,30}$</Property>
            <Property name="UsernameJavaRegExViolationErrorMsg">Username pattern policy violated</Property>
            <Property name="PasswordJavaRegEx">^[\S]{5,30}$</Property>
            <Property name="PasswordJavaScriptRegEx">^[\S]{5,30}$</Property>
            <Property name="PasswordJavaRegExViolationErrorMsg">Password length should be within 5 to 30 characters</Property>
            <Property name="RolenameJavaRegEx">[a-zA-Z0-9._\-|//]{3,30}$</Property>
            <Property name="RolenameJavaScriptRegEx">^[\S]{3,30}$</Property>
            <Property name="SCIMEnabled">true</Property>
            <Property name="IsBulkImportSupported">false</Property>
            <Property name="EmptyRolesAllowed">false</Property>
            <Property name="PasswordHashMethod">PLAIN_TEXT</Property>
            <Property name="MultiAttributeSeparator">,</Property>
            <Property name="MaxUserNameListLength">100</Property>
            <Property name="MaxRoleNameListLength">100</Property>
            <Property name="kdcEnabled">false</Property>
            <Property name="defaultRealmName">WSO2.ORG</Property>
            <Property name="UserRolesCacheEnabled">true</Property>
            <Property name="ConnectionPoolingEnabled">false</Property>
            <Property name="LDAPConnectionTimeout">5000</Property>
            <Property name="ReadTimeout"/>
            <Property name="RetryAttempts"/>
        </UserStoreManager>


También abra el archivo tenant-mgt.xml en <carbon-home>/repository/conf/ y cambie la siguiente línea.::

	<Property name="RootPartition">dc=wso2,dc=com</Property>

Este es el bloque completo::

	<!--When the primary user store is using LDAP user store, in MT mode following tenant manager will be used.-->
	    <TenantManager class="org.wso2.carbon.user.core.tenant.CommonHybridLDAPTenantManager">
		<Property name="MultiTenantRealmConfigBuilder">org.wso2.carbon.user.core.config.multitenancy.CommonLDAPRealmConfigBuilder</Property>
		<Property name="RootPartition">dc=wso2,dc=com</Property>
		<Property name="OrganizationalObjectClass">organizationalUnit</Property>
		<Property name="OrganizationalAttribute">ou</Property>
		<Property name="OrganizationalSubContextObjectClass">organizationalUnit</Property>
		<Property name="OrganizationalSubContextAttribute">ou</Property>
	    </TenantManager>



Inicie el servidor WSO2 IS. Ahora está listo con su openLDAP como su principal fuente de datos.


Muchas gracias






