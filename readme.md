### 1.EmEditorɾ�����У�
> �����滻��^[\s\t]*\n

### 2.Oracle SQLPlus��������ɾ������
> yum install rlwrap
yum install readline-devel
�޸�.bash_profile��ʹ��ͬ��ʸ���ϵͳ����ʹ��ͬ��ʸ���ϵͳ����
vim /home/oracle/.bash_profile  
// stty erase ^H
alias sqlplus='rlwrap sqlplus'  
alias rman='rlwrap rman' 
### 3.Oracle���ݵ���

* 1.����Ŀ¼
 
 >mkdir dataImp  
chown -R oracle:oinstall dataImp
create directory impdp_dir as '/u01/dataImp';
grant read,write on directory impdp_dir to �û���;


* 2.����

>1)����Schema
expdp scott/tiger@orcl schemas=scott dumpfile=expdp.dmp directory=dump_dir; 
2)������
expdp scott/tiger@orcl tables=emp,dept dumpfile=expdp.dmp directory=dump_dir;
3)����ѯ������
expdp scott/tiger@orcl directory=dump_dir dumpfile=expdp.dmp tables=empquery='where deptno=20';
4)����ռ䵼
expdp system/manager@orcl directory=dump_dir dumpfile=tablespace.dmptablespaces=temp,example;
5)���������ݿ�
expdp system/manager@orcl directory=dump_dir dumpfile=full.dmp full=y;

* 3.����
>1)�����û������û�scott���뵽�û�scott��
impdp scott/tiger@orcl directory=dump_dir dumpfile=expdp.dmp schemas=scott;
2)�������scott�û��аѱ�dept��emp���뵽system�û��У�
impdp system/manager@orcl directory=dump_dir dumpfile=expdp.dmptables=scott.dept,scott.emp remap_schema=scott:system;
3)�����ռ�
impdp system/manager@orcl directory=dump_dir dumpfile=tablespace.dmp tablespaces=example;
4)�������ݿ�
impdb system/manager@orcl directory=dump_dir dumpfile=full.dmp full=y;
5)׷������
impdp system/manager@orcl directory=dump_dir dumpfile=expdp.dmp schemas=systemtable_exists_action
### 4.Oracle����txt
* 1.main.sql��
>set linesize 200 
set term off verify off feedback off pagesize 999 
set markup html on entmap ON spool on preformat off
spool ./tables.txt
@./get_tables.sql
spool off
exit

* 2.get_tables.sql:
> select table_name from all_tables where table_name like '%CWBASE%';
* 3.ִ��
> sqlplus / as sysdba @./main.sql