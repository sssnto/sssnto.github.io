---
title: spring��Ҫ���
date: 2017��5��16��13:34:38
tags: spring
---
# Spring��Spring Cloud��Ҫ���

## spring ������Ŀ��
- Spring IO platform:����ϵͳ�����ǿɼ��ɵģ������ִ���Ӧ�õİ汾ƽ̨��������˵����ʹ��maven dependency����spring jar��ʱ�����ڹ����ˡ�
- Spring Boot:ּ�ڼ򻯴�����Ʒ���� Spring Ӧ�úͷ��񣬼��������ļ���ʹ��Ƕ��ʽweb��������������࿪�伴��΢�����ܣ����Ժ�spring cloud���ϲ���
- Spring Framework:��ͨ����˵��spring ��ܣ���һ����Դ��Java/Java EEȫ����ջӦ�ó����ܣ�����spring��Ŀ��spring bootҲ�����ڴ˿�ܡ�
- Spring Cloud��΢���񹤾߰���Ϊ�������ṩ���ڷֲ�ʽϵͳ�����ù��������֡���·��������·�ɡ�΢�����������ߵȿ������߰���
- Spring XD����һ������ʱ������������������ǿ�����ܣ������spring��������spring batch��spring boot��spring data���ɼ������ݲ�����
- Spring Data����һ�����ݷ��ʼ������Ĺ��߰�����װ�˺ܶ������ݼ����ݿ�ķ�����ؼ�����������jdbc��Redis��MongoDB��Neo4j�ȡ�
- Spring Batch���������ܣ���˵����������ִ�й����������ܰ���������ȡ���־��¼/���ٵȡ�
- Spring Security����һ���ܹ�Ϊ����Spring����ҵӦ��ϵͳ�ṩ����ʽ�İ�ȫ���ʿ��ƽ�������İ�ȫ��ܡ�
- Spring Integration��������ҵӦ�ü��ɣ�EAI/ESB���ı�̿�ܣ�֧�ֵ�ͨ�ŷ�ʽ����HTTP��FTP��TCP/UDP��JMS��RabbitMQ��Email�ȡ�
- Spring Social��һ�鹤�߰���һ�������罻����API����Twitter��Facebook��LinkedIn��GitHub�ȣ��м�ʮ����
- Spring AMQP����Ϣ���в����Ĺ��߰�����Ҫ�Ƿ�װ��RabbitMQ�Ĳ�����
- Spring HATEOAS����һ������֧��ʵ�ֳ��ı������� REST Web ����Ŀ����⡣
- Spring Mobile����Spring MVC����չ���������ֻ��ϵ�WebӦ�ÿ�����
- Spring for Android����Spring��ܵ�һ����չ������ҪĿ���ں���Android����Ӧ�õĿ������ṩRestTemplate������Rest����
- Spring Web Flow��Ŀ���ǳ�Ϊ����WebӦ��ҳ�����̵���ѷ�������ҳ����ת���̵��������������á�
- Spring LDAP����һ�����ڲ���LDAP��Java���߰�������Spring��JdbcTemplateģʽ����LDAP���ʡ�
- Spring Session��session����Ŀ������߰���������԰�session���浽redis�ȣ����м�Ⱥ��session����
- Spring Web Services���ǻ���Spring��Web�����ܣ��ṩSOAP���񿪷�������ͨ�����ַ�ʽ����Web����
- Spring Shell���ṩ����ʽ��Shell������ʹ�ü򵥵Ļ���Spring�ı��ģ���������������Spring Roo���
- Spring Roo����һ��Spring�����ĸ������ߣ�ʹ�������в����������Զ�����Ŀ�������ǳ�������Rails��
- Spring Scala��ΪScala���Ա���ṩ��spring��ܵķ�װ���µı�����ԣ�Javaƽ̨��Scala��2003���/2004�����������
- Spring BlazeDS Integration��һ������RIA���߰������Լ���Adobe Flex��BlazeDS��Spring�Լ�Java��������RIA��
- Spring Loaded������ʵ��java�����webӦ�õ��Ȳ���Ŀ�Դ���ߡ�
- Spring REST Shell�����Ե���Rest����������й��ߣ��������в���Rest����


## Ŀǰ��˵spring��Ҫ������spring boot�����ڿ���΢���񣩺�spring cloud��ؿ�ܵĿ�����spring cloud����Ŀ������


- Spring Cloud Config�����ù��������߰���������������÷ŵ�Զ�̷�������Ŀǰ֧�ֱ��ش洢��Git�Լ�Subversion��
- Spring Cloud Bus���¼�����Ϣ���ߣ������ڼ�Ⱥ�����磬���ñ仯�¼����д���״̬�仯������Spring Cloud Config����ʵ���Ȳ���
- Spring Cloud Netflix����Զ���Netflix����ṩ�Ŀ������߰������а���Eureka��Hystrix��Zuul��Archaius�ȡ�
- Netflix Eureka���ƶ˸��ؾ��⣬һ������ REST �ķ������ڶ�λ������ʵ���ƶ˵ĸ��ؾ�����м��������Ĺ���ת�ơ�
- Netflix Hystrix���ݴ�����ߣ�ּ��ͨ�����Ʒ���͵�������Ľڵ�,�Ӷ����ӳٺ͹����ṩ��ǿ����ݴ�������
- Netflix Zuul����Ե���񹤾ߣ����ṩ��̬·�ɣ���أ����ԣ���ȫ�ȵı�Ե����
- Netflix Archaius�����ù���API������һϵ�����ù���API���ṩ��̬���ͻ����ԡ��̰߳�ȫ���ò�������ѯ��ܡ��ص����Ƶȹ��ܡ�
- Spring Cloud for Cloud Foundry��ͨ��Oauth2Э��󶨷���CloudFoundry��CloudFoundry��VMware�Ƴ��Ŀ�ԴPaaS��ƽ̨��
- Spring Cloud Sleuth����־�ռ����߰�����װ��Dapper,Zipkin��HTrace������
- Spring Cloud Data Flow�������ݲ������ߣ�ͨ�������з�ʽ������������
- Spring Cloud Security����ȫ���߰���Ϊ���Ӧ�ó�����Ӱ�ȫ���ƣ���Ҫ��ָOAuth2��
- Spring Cloud Consul����װ��Consul������consul��һ�������������ù��ߣ���Docker���������޷켯�ɡ�
- Spring Cloud Zookeeper������Zookeeper�Ĺ��߰�������ʹ��zookeeper��ʽ�ķ���ע��ͷ��֡�
- Spring Cloud Stream����������������������װ����Redis,Rabbit��Kafka�ȷ��ͽ�����Ϣ��
- Spring Cloud CLI������ Spring Boot CLI�����������������з�ʽ���ٽ����������