页面头部增加标签

```
<%@ taglib prefix="fmt" uri="http://java.sun.com/jsp/jstl/fmt" %>
```

前端将date日期转换为年月日

```
<td>
	<%--前端将date日期转换为年月日--%>
	<fmt:formatDate pattern="yyyy年MM月dd日" value="${employee.join_date}"/>
</td>
```

