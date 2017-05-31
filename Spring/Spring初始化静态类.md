```xml
xmlns:util="http://www.springframework.org/schema/util"
http://www.springframework.org/schema/util
http://www.springframework.org/schema/util/spring-util-4.0.xsd"

<bean
	class="org.springframework.beans.factory.config.MethodInvokingFactoryBean">
	<property name="targetObject">
		<!-- System.getProperties() -->
		<bean
			class="org.springframework.beans.factory.config.MethodInvokingFactoryBean">
			<property name="targetClass" value="java.lang.System" />
			<property name="targetMethod" value="getProperties" />
		</bean>
	</property>
	<property name="targetMethod" value="putAll" />
	<property name="arguments">
		<!-- The new Properties -->
		<util:properties>
			<prop key="driver">${driver}</prop>
			<prop key="url">${url}</prop>
			<prop key="username">${username}</prop>
			<prop key="password">${password}</prop>
		</util:properties>
	</property>
</bean>
```