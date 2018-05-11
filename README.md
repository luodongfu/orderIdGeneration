分布式环境订单ID生成方案

方案一：UUID(唯一通用识别码)
	组成：当前日期+时间+时钟序列+机器识别号（Mac地址或其他）。
	在分布式系统中，所有元素都不需要通过中央控制端来判断数据唯一性。

方案二：数据库自增
	关系型数据库都实现数据库自增ID；MySQL通过AUTO_INCREMENT实现、Oracle通过Sequence序列实现。
	在数据库集群环境下，不同数据库节点可设置不同起步值、相同步长来实现集群下生成全局唯一、递增ID。
	MySQL：
   	  SET GLOBAL auto_increment_increment=3;
	  SET GLOBAL auto_increment_offset=1;

方案三：Snowflake(雪花算法)
	组成：41位时间戳+10位机器ID+12位序列号（自增），转换为长度为18的长整型。
	Twitter为满足每秒上万条消息的创建，每条消息都必须分配全局唯一ID，这些ID需要趋势递增，方便客户端排序。

方案四：Redis(incr自增ID)
	Redis实现了incr(key) API用于将key的值递增1，并返回结果。
	如果key不存在，则创建并赋值为0，然后再执行incr操作。

总结：
  策略			优点						缺点					格式
 UUID		实现简单、不占用宽带			无序、不可读、查询慢		32位
 DB自增		无代码、递增				DB单点故障、扩展性瓶颈	
 Snowflake	不占用宽带、低位趋势递增		依赖服务器时间			18位
 Redis		无单点故障、性能优于DB、递增	占用宽带、Redis集群维护	12位