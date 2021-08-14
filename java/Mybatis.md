# Mybatis

## Mybatis 枚举处理

```java
public interface BaseEnum {

    /**
     * 通过枚举名称 <code>name</code> 获取枚举
     *
     * @param target
     * @param name
     * @param <T>
     * @return
     */
    static <T extends Enum<?> & BaseEnum> T of(Class<T> target, String name) {
        T[] values = target.getEnumConstants();
        for (T t : values) {
            if (name.equalsIgnoreCase(t.getName())) {
                return t;
            }
        }
        throw new EnumConstantNotPresentException(target, name);
    }

    /**
     * 通过枚举数字 <code>code</code> 获取枚举
     *
     * @param target
     * @param code
     * @param <T>
     * @return
     */
    static <T extends Enum<?> & BaseEnum> T of(Class<T> target, Integer code) {
        T[] values = target.getEnumConstants();
        for (T t : values) {
            if (code.equals(t.getCode())) {
                return t;
            }
        }
        throw new EnumConstantNotPresentException(target, String.valueOf(code));
    }

    Integer getCode();

    String getName();
}

``` java
@MappedTypes(BaseEnum.class)
public class MybatisBaseEnumTypeHandler<T extends Enum<?> & BaseEnum> extends BaseTypeHandler<BaseEnum> {

    private Class<T> type;

    public MybatisBaseEnumTypeHandler(Class<T> type) {
        if (type == null) {
            throw new IllegalArgumentException("Type argument cannot be null");
        }
        this.type = type;
    }

    @Override
    public void setNonNullParameter(PreparedStatement ps, int i, BaseEnum parameter, JdbcType jdbcType) throws SQLException {
        ps.setInt(i, parameter.getCode());
    }

    @Override
    public BaseEnum getNullableResult(ResultSet rs, String columnName) throws SQLException {
        int code = rs.getInt(columnName);
        return rs.wasNull() ? null : codeOf(code);
    }

    @Override
    public BaseEnum getNullableResult(ResultSet rs, int columnIndex) throws SQLException {
        int code = rs.getInt(columnIndex);
        return rs.wasNull() ? null : codeOf(code);
    }

    @Override
    public BaseEnum getNullableResult(CallableStatement cs, int columnIndex) throws SQLException {
        int code = cs.getInt(columnIndex);
        return cs.wasNull() ? null : codeOf(code);
    }

    private T codeOf(int code) {
        try {
            return BaseEnum.of(type, code);
        } catch (Exception ex) {
            throw new IllegalArgumentException("Cannot convert " + code + " to " + type.getSimpleName() + " by code value.", ex);
        }
    }
}
```

```yml
# Mybatis配置
mybatis:
  # 搜索指定包别名
  typeAliasesPackage: com.xxx.domain
  # 配置mapper的扫描，找到所有的mapper.xml映射文件
  mapperLocations: classpath*:**/*Mapper.xml
  # 注册自定义BaseEnum处理器
  typeHandlersPackage: com.xxx.package
  # mybatis配置
  configLocation: classpath:mybatis-config.xml
```

## JSON Converter

```java
public class BaseEnumConverterFactory<T extends Enum<?> & BaseEnum> implements ConverterFactory<String, T> {

    @Override
    public <E extends T> Converter<String, E> getConverter(Class<E> aClass) {
        return new BaseEnumConverter<>(aClass);
    }

    private static class BaseEnumConverter<T extends Enum<?> & BaseEnum> implements Converter<String, T> {

        private final Class<T> enumType;

        public BaseEnumConverter(Class<T> enumType) {
            this.enumType = enumType;
        }

        @Override
        public T convert(String s) {
            try {
                return BaseEnum.of(enumType, s);
            } catch (EnumConstantNotPresentException e) {
                return BaseEnum.of(enumType, Integer.valueOf(s));
            }
        }
    }

}

@Configuration
public class ApplicationConfig implements WebMvcConfigurer {

    @Override
    public void addFormatters(FormatterRegistry registry) {
        registry.addConverterFactory(new BaseEnumConverterFactory<>());
    }
}

```

[1](https://feiyizhan.github.io/mybatis%20/2019/05/29/MyBatis-TypeHandler%E7%9A%84%E7%AC%94%E8%AE%B0.html)
[2](https://yulaiz.com/java-mysql-enum/)
[3](https://segmentfault.com/a/1190000010755321)
