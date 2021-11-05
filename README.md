# PostgreSQL + 身份证号

本项目提供了一系列用于处理身份证号的PostgreSQL存储过程。

详细内容：[sql/api.sql](sql/api.sql)

```sql
COMMENT ON FUNCTION sfz_adcode(id text) IS '提取6位地区代码，输入18/15位字符身份证号，返回6位整数区划代码';
COMMENT ON FUNCTION sfz_birthday(id text) IS '提取8位出生日期，输入18/15位字符身份证号，返回出生日期，非法日期返回空';
COMMENT ON FUNCTION sfz_sequence(id text) IS '提取3位序列号，输入18/15位字符身份证号，返回3位序列号字符';
COMMENT ON FUNCTION sfz_is_male(id text) IS '输入18/15位字符身份证号，返回是否代表男性';
COMMENT ON FUNCTION sfz_is_female(id text) IS '输入18/15位字符身份证号，返回是否代表女性';
COMMENT ON FUNCTION sfz_checksum(id text) IS '提取身份证末位校验和，15位身份证号返回空';
COMMENT ON FUNCTION sfz_calculate_checksum(id text) IS '身份证号校验和，输入为17/18位身份证号，计算得到最后一位校验和';
COMMENT ON FUNCTION sfz_valid_v2(id text) IS '检查是否为合法18位二代身份证号：长度，日期，校验和。地区码暂不检查';
COMMENT ON FUNCTION sfz_valid_v1(id text) IS '检查是否为合法15位一代身份证号：长度，日期。地区码暂不检查';
COMMENT ON FUNCTION sfz_new(INTEGER,DATE,INTEGER) IS '使用地区码，生日，序列号创建18位二代身份证';
COMMENT ON FUNCTION sfz_upgrade(id text) IS '将一个合法的15位身份证升级为18位二代身份证';
COMMENT ON FUNCTION sfz_new(INTEGER,DATE,INTEGER) IS '生成一个随机的符合语法语义的身份证号';

```


## 身份证ID结构说明

身份证号包含的信息：
* 空间：6位行政区划
* 时间：出生日期8位（二代），6位（一代）
* 顺序：同地区同日期内出生人的序号
* 性别：奇数顺序码为男性，偶数为女性
* 校验吗：对前17位校验求和模11的余数，余10则为X

**一代身份证**

长度为15位整数，由三部分组成：

6位区划   6位生日  3位序号

610204 - 541216 - 180


**二代身份证**

长度为18位整数，由四部分组成：

6位区划    8位生日   3位序号 1位校验码

610204 - 19541216 - 180 - 8


## 基本功能
* 信息提取
  * 区划代码
  * 出生日期
  * 序列号
  * 性别
  * 校验和
* 形式验证
  * 是否合法一代证号
  * 是否合法二代证号
  * 计算校验和
* 拼接创建：
  * 升级一代证号
  * 创建新二代证号
  * 生成随机合规二代证号

  