# Linux��http��ʽ�git������

linux��http��ʽ�git����������clone�ķ�ʽΪgit clone http://xxxxxxxx��

�õ�apache��git-core��֧��git��CGI��

1.��װhttpd
```
yum install httpd
```

��Ϊ�ܶ�����������������80�˿ڣ������޸ļ����˿ڣ�

```
vi /etc/httpd/conf/httpd.conf

// �ҵ� Listen 80�޸�Ϊ��
Listen 8081
```

�����������������ʾ��Redirecting to /bin/systemctl start  httpd.service������/bin/systemctl start  httpd.service����
```
service httpd start
```
����������ʣ�http://192.168.1.55:8081��������ʲ��ˣ�������Ϊ����ǽ�������
```
// ������з���8081�˿�
iptables -A INPUT -p tcp --dport 8081 -j ACCEPT

// ��������
service iptables restart

/etc/rc.d/init.d/iptables save

// ����ǰ����Ƶķ����������淽���� iptables-save > /etc/sysconfig/iptables
```
2.��װgit
```
yum install git
yum install git-core
```

�����ֿ⣺
```
mkdir -p /opt/http_git/test.git
cd /opt/http_git/test.git
git init --bare

// ����Ȩ��
chown -R apache:apache /opt/http_git
```
�����˺ţ�
```
// testuserΪ�˻��� �������ⶨ��
htpasswd -m -c /etc/httpd/conf.d/git-team.htpasswd testuser

// �޸�git-team.htpasswd�ļ���������������Ⱥ��
chown apache:apache /etc/httpd/conf.d/git-team.htpasswd

// ����git-team.htpasswd�ļ��ķ���Ȩ��
chmod 640 /etc/httpd/conf.d/git-team.htpasswd
```
����apache��ʹ������ת����git-cgi��
```
vi /etc/httpd/conf/httpd.conf
```
```
// �����һ��IncludeOptional conf.d/*.conf����������������ݣ�
<VirtualHost *:8081>
        ServerName 192.168.1.55
        SetEnv GIT_HTTP_EXPORT_ALL
        SetEnv GIT_PROJECT_ROOT /opt/http_git
        ScriptAlias /git/ /usr/libexec/git-core/git-http-backend/
        <Location />
                AuthType Basic
                AuthName "Git"
                AuthUserFile /etc/httpd/conf.d/git-team.htpasswd
                Require valid-user
        </Location>
</VirtualHost>

///
ServerName��git������������,���������д����ӵ�IP
/opt/http_git�Ǵ�����ŵ��ļ���
ScriptAlias�ǽ���/git/��ͷ�ķ���·��ӳ����git��CGI����git-http-backend
AuthUserFile����֤�û��ʻ����ļ�
```
����������
```
service httpd restart
```
3.�ͻ���clone���
```
git clone http://192.168.1.55:8081/git/test.git
```
����ʾ�����˺����룬�˺�Ϊ�������õ�testuser 
