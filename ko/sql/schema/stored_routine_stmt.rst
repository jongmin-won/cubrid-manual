
:meta-keywords: procedure definition, create procedure, drop procedure, function definition, create function, drop function
:meta-description: Define functions/procedures in CUBRID database using create procedure, create function, drop procedure and drop function statements.


*************************
저장 함수/프로시저 정의문
*************************

.. _create-procedure:

CREATE PROCEDURE
=================

**CREATE PROCEDURE** 문을 사용하여 저장 프로시저를 등록한다. CUBRID는 SQL을 절차적 언어의 형식으로 사용할 수 있는 PL/CSQL과 Java 저장 프로시저를 지원한다.

자세한 내용은 :ref:`sql_procedural_langauge`\를 참고한다.

::

    CREATE [OR REPLACE] PROCEDURE procedure_name [(<parameter_definition> [, <parameter_definition>] ...)]
    [<authid>] {IS | AS} LANGUAGE <lang> <body> <comment>;
    
        <parameter_definition> ::= parameter_name [IN|OUT|IN OUT|INOUT] sql_type [COMMENT 'param_comment_string']
	<authid> ::= [AUTHID DEFINER|AUTHID OWNER|AUTHID CALLER|AUTHID CURRENT_USER]
        <lang> ::= [PLCSQL|JAVA|]
        <java_call_specification> ::= NAME 'java_method_name (java_type [,java_type]...) [return java_type]'

*   *procedure_name*: 생성할 저장 프로시저의 이름을 지정한다(최대 254바이트).
*   *parameter_name*: 인자의 이름을 지정한다(최대 254바이트).
*   *sql_type*: 인자의 데이터 타입을 지정한다. 지정할 수 있는 데이터 타입은 :ref:`jsp-type-mapping`\을 참고한다.
*   *param_comment_string*: 인자 커멘트 문자열을 지정한다.
*   *authid*: 저장 프로시저의 실행 권한을 지정한다. 기본값은 AUTHID OWNER이다.
*   *sp_comment_string*: 저장 프로시저의 커멘트 문자열을 지정한다.
*   *java_method_name*: 자바의 클래스 이름을 포함하여 자바의 메소드 이름을 지정한다.
*   *java_type*: 자바의 데이터 타입을 지정한다. 지정할 수 있는 데이터 타입은 :ref:`jsp-type-mapping`\을 참고한다.

로드한 자바 클래스에 대해 SQL 문이나 Java 응용 프로그램에서 클래스 내의 함수를 어떻게 호출할지 모르기 때문에 Java Call Specification (<*java_call_specification*>)를 사용하여 등록해야 한다.
Java Call Specification 작성 방법에 대해서는 :ref:`call-specification`\을 참조한다.

저장 프로시저의 커멘트
----------------------------------

저장 프로시저의 커멘트를 다음과 같이 제일 뒤에 지정할 수 있다. 

.. code-block:: sql


    CREATE FUNCTION Hello() RETURN VARCHAR
    AS LANGUAGE JAVA
    NAME 'SpCubrid.HelloCubrid() return java.lang.String'
    COMMENT 'function comment';

저장 프로시저의 인자 뒤에는 다음과 같이 지정할 수 있다.

.. code-block:: sql

    CREATE OR REPLACE FUNCTION test(i in number COMMENT 'arg i') 
    RETURN NUMBER AS LANGUAGE JAVA NAME 'SpTest.testInt(int) return int' COMMENT 'function test';

저장 프로시저의 커멘트는 다음 구문을 실행하여 확인할 수 있다.

.. code-block:: sql

    SELECT sp_name, comment FROM db_stored_procedure; 

저장 프로시저 인자의 커멘트는 다음 구문을 실행하여 확인할 수 있다.

.. code-block:: sql
          
    SELECT sp_name, arg_name, comment FROM db_stored_procedure_args;


등록된 저장 프로시저의 정보 확인
------------------------------------------

등록된 저장 프로시저의 정보는 **db_stored_procedure** 시스템 가상 클래스와 **db_stored_procedure_args** 시스템 가상 클래스에서 확인할 수 있다. 
**db_stored_procedure** 시스템 가상 클래스에서는 저장 프로시저의 이름과 타입, 인자의 수, Java 클래스에 대한 명세, 저장 프로시저의 소유자에 대한 정보를 확인할 수 있다.
**db_stored_procedure_args** 시스템 가상 클래스에서는 저장 프로시저에서 사용하는 인자에 대한 정보를 확인할 수 있다.

.. code-block:: sql

    SELECT * FROM db_stored_procedure WHERE sp_type = 'PROCEDURE';
    
::
    
    sp_name               sp_type               return_type             arg_count  lang target                owner
    ================================================================================
    'athlete_add'         'PROCEDURE'           'void'                          4  'JAVA''Athlete.Athlete(java.lang.String, java.lang.String, java.lang.String, java.lang.String)'  'DBA'

.. code-block:: sql
    
    SELECT * FROM db_stored_procedure_args WHERE sp_name = 'athlete_add';
    
::
    
    sp_name   index_of  arg_name  data_type      mode
    =================================================
     'athlete_add'                   0  'name'                'STRING'              'IN'
     'athlete_add'                   1  'gender'              'STRING'              'IN'
     'athlete_add'                   2  'nation_code'         'STRING'              'IN'
     'athlete_add'                   3  'event'               'STRING'              'IN'


DROP PROCEDURE
==============

CUBRID에서는 등록한 저장 프로시저를 **DROP PROCEDURE** 구문을 사용하여 삭제할 수 있다.
이 때, 여러 개의 *procedure_name* 을 콤마(,)로 구분하여 한꺼번에 여러 개의 저장 프로시저를 삭제할 수 있다.

::

    DROP PROCEDURE procedure_name [{ , procedure_name , ... }]

*   *procedure_name*: 제거할 프로시저의 이름을 지정한다.

.. code-block:: sql

    DROP PROCEDURE hello, sp_int;

저장 프로시저의 삭제는 프로시저를 등록한 사용자와 DBA의 구성원만 삭제할 수 있다.
예를 들어 'sp_int' 저장 프로시저를 **PUBLIC** 이 등록했다면, **PUBLIC** 또는 **DBA** 의 구성원만이 'sp_int' 저장 프로시저를 삭제할 수 있다.

.. _create-function:

CREATE FUNCTION
=================

**CREATE FUNCTION** 문을 사용하여 저장 함수를 등록한다.
CUBRID는 Java를 제외한 다른 언어에서는 저장 함수를 지원하지 않는다. CUBRID에서 저장 함수는 오직 Java로만 구현 가능하다.
등록한 저장 함수의 사용 방법은 :doc:`/sql/jsp`\를 참고한다.

::

    CREATE [OR REPLACE] FUNCTION function_name [(<parameter_definition> [, <parameter_definition>] ...)] RETURN sql_type
    [<authid_and_deterministic>] {IS | AS} LANGUAGE JAVA <java_call_specification>
    COMMENT 'sp_comment_string';
    
        <parameter_definition> ::= parameter_name [IN|OUT|IN OUT|INOUT] sql_type [COMMENT 'param_comment_string']
	<authid_and_deterministic> ::=
	    <authid> = [AUTHID DEFINER|AUTHID OWNER|AUTHID CALLER|AUTHID CURRENT_USER]
	    | <deterministic> = [NOT DETERMINISTIC|DETERMINISTIC]
        <java_call_specification> ::= NAME 'java_method_name (java_type [,java_type]...) [return java_type]'

*   *function_name*: 생성할 저장 함수의 이름을 지정한다(최대 254바이트).
*   *parameter_name*: 인자의 이름을 지정한다(최대 254바이트).
*   *sql_type*: 인자 또는 리턴 값의 데이터 타입을 지정한다. 지정할 수 있는 데이터 타입은 :ref:`jsp-type-mapping`\을 참고한다.
*   *param_comment_string*: 인자 커멘트 문자열을 지정한다.
*   *authid*: 저장 함수의 실행 권한을 지정한다. deterministic 키워드와 순서와 상관없이 함께 사용할 수 있다. 기본값은 AUTHID OWNER이다.
*   *deterministic*: 저장 함수를 상관 부질의에서 사용하는 경우, 부질의 결과를 캐시하여 최적화하는 용도로 지정한다. authid 키워드와 순서와 관계없이 함께 사용할 수 있다. 기본값은 NOT DETERMINISTIC이다.
*   *sp_comment_string*: 저장 함수의 커멘트 문자열을 지정한다.
*   *java_method_name*: 자바의 클래스 이름을 포함하여 자바의 메소드 이름을 지정한다.
*   *java_type*: 자바의 데이터 타입을 지정한다. 지정할 수 있는 데이터 타입은 :ref:`jsp-type-mapping`\을 참고한다.

로드한 자바 클래스에 대해 SQL 문이나 Java 응용 프로그램에서 클래스 내의 함수를 어떻게 호출할지 모르기 때문에 Java Call Specification (<*java_call_specification*>)를 사용하여 등록해야 한다.
Java Call Specification 작성 방법에 대해서는 :ref:`call-specification`\을 참조한다.

저장 함수의 커멘트
----------------------------------

저장 함수의 커멘트를 다음과 같이 제일 뒤에 지정할 수 있다. 

.. code-block:: sql

    CREATE FUNCTION Hello() RETURN VARCHAR
    AS LANGUAGE JAVA
    NAME 'SpCubrid.HelloCubrid() return java.lang.String'
    COMMENT 'function comment';

저장 함수의 인자 뒤에는 다음과 같이 지정할 수 있다.

.. code-block:: sql

    CREATE OR REPLACE FUNCTION test(i in number COMMENT 'arg i') 
    RETURN NUMBER AS LANGUAGE JAVA NAME 'SpTest.testInt(int) return int' COMMENT 'function test';

저장 함수의 커멘트는 다음 구문을 실행하여 확인할 수 있다.

.. code-block:: sql

    SELECT sp_name, comment FROM db_stored_procedure; 

함수 인자의 커멘트는 다음 구문을 실행하여 확인할 수 있다.

.. code-block:: sql
          
    SELECT sp_name, arg_name, comment FROM db_stored_procedure_args;


등록된 저장 함수의 정보 확인
------------------------------------------

등록된 저장 함수의 정보는 **db_stored_procedure** 시스템 가상 클래스와 **db_stored_procedure_args** 시스템 가상 클래스에서 확인할 수 있다. 
**db_stored_procedure** 시스템 가상 클래스에서는 저장 함수의 이름과 타입, 반환 타입, 인자의 수, Java 클래스에 대한 명세, 저장 함수의 소유자에 대한 정보를 확인할 수 있다. 
**db_stored_procedure_args** 시스템 가상 클래스에서는 저장 함수에서 사용하는 인자에 대한 정보를 확인할 수 있다.

.. code-block:: sql

    SELECT * FROM db_stored_procedure WHERE sp_type = 'FUNCTION';
    
::
    
    sp_name               sp_type               return_type             arg_count  lang target                owner
    ================================================================================
    'hello'               'FUNCTION'            'STRING'                        0  'JAVA''SpCubrid.HelloCubrid() return java.lang.String'  'DBA'
     
    'sp_int'              'FUNCTION'            'INTEGER'                       1  'JAVA''SpCubrid.SpInt(int) return int'  'DBA'

.. code-block:: sql
    
    SELECT * FROM db_stored_procedure_args WHERE sp_name = 'sp_int';
    
::
    
    sp_name   index_of  arg_name  data_type      mode
    =================================================
     'sp_int'                        0  'i'                   'INTEGER'             'IN'

CREATE FUNCTION DETERMINISTIC
------------------------------------------

저장 함수 생성 시 DETERMINISTIC 키워드를 명시할 수 있다.
DETERMINISTIC 키워드를 사용한 저장 함수를 상관 부질의에서 사용할 경우, 부질의 결과를 캐시하여 성능을 최적화할 수 있다.

다음은 DETERMINISTIC을 사용한 저장 함수의 예시이다. 이 예시에서는 상관 부질의를 사용할 때 결과를 캐시하여 성능을 최적화하는 과정을 보여준다.

.. code-block:: sql

    CREATE TABLE dummy_tbl (col1 INTEGER);
    INSERT INTO dummy_tbl VALUES (1), (2), (1), (2);

    CREATE OR REPLACE FUNCTION pl_csql_not_deterministic (n INTEGER) RETURN INTEGER AS
    BEGIN
      return n + 1;
    END;

    CREATE OR REPLACE FUNCTION pl_csql_deterministic (n INTEGER) RETURN INTEGER DETERMINISTIC AS
    BEGIN
      return n + 1;
    END;

    SELECT sp_name, owner, sp_type, is_deterministic from db_stored_procedure;

::
    
    sp_name                      owner           sp_type               is_deterministic    
 ========================================================================================
    'pl_csql_not_deterministic'  'DBA'           'FUNCTION'            'NO'                
    'pl_csql_deterministic'      'DBA'           'FUNCTION'            'YES' 

위 예시에서 pl_csql_not_deterministic 함수는 NOT DETERMINISTIC이므로 상관 부질의에서 캐시를 사용하지 않는다.
반면, pl_csql_deterministic 함수는 DETERMINISTIC 키워드가 지정되어 있으므로 상관 부질의 결과를 캐시하여 성능을 최적화할 수 있다.

.. code-block:: sql
    
    ;trace on
    SELECT (SELECT pl_csql_not_deterministic (t1.col1) FROM dual) AS results FROM dummy_tbl t1;

::

      results
 =============
            2
            3
            2
            3
 
 === Auto Trace ===
    ...
    Trace Statistics:
      SELECT (time: 3, fetch: 44, fetch_time: 0, ioread: 0)
        SCAN (table: dba.dummy_tbl), (heap time: 0, fetch: 20, ioread: 0, readrows: 4, rows: 4)
        SUBQUERY (correlated)
          SELECT (time: 3, fetch: 24, fetch_time: 0, ioread: 0)
            SCAN (table: dual), (heap time: 0, fetch: 16, ioread: 0, readrows: 4, rows: 4)

pl_csql_not_deterministic 함수는 NOT DETERMINISTIC이므로 부질의 결과를 캐시하지 않는다.

.. code-block:: sql
    
    ;trace on
    SELECT (SELECT pl_csql_deterministic (t1.col1) FROM dual) AS results FROM dummy_tbl t1;

::

      results
 =============
            2
            3
            2
            3

 === Auto Trace ===
    ...
    Trace Statistics:
      SELECT (time: 3, fetch: 36, fetch_time: 0, ioread: 0)
        SCAN (table: dba.dummy_tbl), (heap time: 0, fetch: 20, ioread: 0, readrows: 4, rows: 4)
        SUBQUERY (correlated)
          SELECT (time: 3, fetch: 16, fetch_time: 0, ioread: 0)
            SCAN (table: dual), (heap time: 0, fetch: 8, ioread: 0, readrows: 2, rows: 2)
            SUBQUERY_CACHE (hit: 2, miss: 2, size: 150808, status: enabled)

pl_csql_deterministic 함수의 Trace 결과에서는 SUBQUERY_CACHE 항목이 표시되며, 첫 번째 결과 (2), (3)은 캐시에서 miss되었고, 이후 동일한 결과부터는 캐시에서 hit된 것을 확인할 수 있다.


DROP FUNCTION
==============

CUBRID에서는 등록한 저장 함수를 **DROP FUNCTION** 구문을 사용하여 삭제할 수 있다.
이 때, 여러 개의 *function_name* 을 콤마(,)로 구분하여 한꺼번에 여러 개의 저장 함수를 삭제할 수 있다.

::

    DROP FUNCTION function_name [{ , function_name , ... }];

*   *function_name*: 제거할 함수의 이름을 지정한다.

.. code-block:: sql

    DROP FUNCTION hello, sp_int;

저장 함수의 삭제는 함수를 등록한 사용자와 DBA의 구성원만 삭제할 수 있다.
예를 들어 'sp_int' 저장 함수를 **PUBLIC** 이 등록했다면, **PUBLIC** 또는 **DBA** 의 구성원만이 'sp_int' 저장 함수를 삭제할 수 있다.


.. _call-specification:

Java Call Specification
==========================

Java 클래스를 로딩했을 때 SQL 문이나 Java 응용 프로그램에서 클래스 내의 함수를 어떻게 호출할지 모르기 때문에 
Java 저장 함수/프로시저를 사용하기 위해서는 Call Specification를 사용하여 등록해야 한다.

Call Specification는 Java 함수 이름과 인자 타입 그리고 리턴 값과 리턴 값의 타입을 SQL 문이나 Java 응용프로그램에서 접근할 수 있도록 해주는 역할을 한다.
Call Specification를 작성하는 구문은 :ref:`create-procedure` 또는 :ref:`create-function` 구문을 사용하여 작성한다.

* Java 저장 함수/프로시저의 이름은 대소문자를 구별하지 않는다. 
* Java 저장 함수/프로시저 이름의 최대 길이는 254바이트이다.
* 하나의 Java 저장 함수/프로시저가 가질 수 있는 인자의 최대 개수는 64개이다.

Java 저장 함수/프로시저의 인자를 **OUT** 으로 설정한 경우 길이가 1인 1차원 배열로 전달된다.
그러므로 Java 메서드는 배열의 첫번째 공간에 전달할 값을 저장해야 한다.

.. code-block:: sql

    CREATE PROCEDURE test_out(x OUT STRING)
    AS LANGUAGE JAVA
    NAME 'SpCubrid.outTest(java.lang.String[] o)';

.. _jsp-type-mapping:

데이터 타입 매핑
----------------

Java 저장 함수/프로시저를 등록할 때, Java 저장 함수/프로시저의 반환 정의와 Java 파일의 선언부의 반환 정의가 일치하는지에 대해서는 검사하지 않는다.
따라서, Java 저장 함수/프로시저의 경우 등록할 때의 반환 정의를 따르고, Java 파일 선언부의 반환 정의는 사용자 정의 정보로서만 의미를 가지게 된다.

Call Specification에서는 SQL의 데이터 타입과 Java의 매개변수, 리턴 값의 데이터 타입이 맞게 대응되어야 한다.
또한 Java 저장함수/프로시저 구현 시, 질의 결과 (ResultSet)의 데이터 타입과 Java의 데이터 타입이 맞게 대응되어야 한다.
CUBRID에서 허용되는 SQL과 Java의 데이터 타입의 관계는 다음의 표와 같다.

**데이터 타입 매핑**

    +------------------------+--------------------------+-------------------------------------------------------------------------+
    | Category               | SQL Type                 | Java Type                                                               |
    +========================+==========================+=========================================================================+
    | Numeric Types          | SHORT, SMALLINT          | short, java.lang.Short                                                  |
    |                        +--------------------------+-------------------------------------------------------------------------+
    |                        | INT, INTEGER             | int, java.lang.Integer                                                  |
    |                        +--------------------------+-------------------------------------------------------------------------+
    |                        | BIGINT                   | long, java.lang.Long                                                    |
    |                        +--------------------------+-------------------------------------------------------------------------+
    |                        | NUMERIC, DECIMAL         | java.math.BigDecimal                                                    |
    |                        +--------------------------+-------------------------------------------------------------------------+
    |                        | FLOAT, REAL              | float, java.lang.Float                                                  |
    |                        +--------------------------+-------------------------------------------------------------------------+
    |                        | DOUBLE, DOUBLE PRECISION | double, java.lang.Double                                                |
    +------------------------+--------------------------+-------------------------------------------------------------------------+
    | Date/Time Types        | DATE                     | java.sql.Date                                                           |
    |                        +--------------------------+-------------------------------------------------------------------------+
    |                        | TIME                     | java.sql.Time                                                           |
    |                        +--------------------------+-------------------------------------------------------------------------+
    |                        | TIMESTAMP                | java.sql.Timestamp                                                      |
    |                        +--------------------------+-------------------------------------------------------------------------+
    |                        | DATETIME                 | java.sql.Timestamp                                                      |
    |                        +--------------------------+-------------------------------------------------------------------------+
    |                        | TIMESTAMPLTZ             | X (not supported)                                                       |
    |                        +--------------------------+-------------------------------------------------------------------------+
    |                        | TIMESTAMPTZ              | X (not supported)                                                       |
    |                        +--------------------------+-------------------------------------------------------------------------+
    |                        | DATETIMELTZ              | X (not supported)                                                       |
    |                        +--------------------------+-------------------------------------------------------------------------+
    |                        | DATETIMETZ               | X (not supported)                                                       |
    +------------------------+--------------------------+-------------------------------------------------------------------------+
    | Bit String  Types      | BIT                      | X (not supported)                                                       |
    |                        +--------------------------+-------------------------------------------------------------------------+
    |                        | VARBIT                   | X (not supported)                                                       |
    +------------------------+--------------------------+-------------------------------------------------------------------------+
    | Character String Types | CHAR                     | java.lang.String                                                        |
    |                        +--------------------------+-------------------------------------------------------------------------+
    |                        | VARCHAR                  | java.lang.String                                                        |
    +------------------------+--------------------------+-------------------------------------------------------------------------+
    | Enum Type              | ENUM                     | X (not supported)                                                       |
    +------------------------+--------------------------+-------------------------------------------------------------------------+
    | LOB Types              | CLOB, BLOB               | X (not supported)                                                       |
    +------------------------+--------------------------+-------------------------------------------------------------------------+
    | Collection Types       | SET, MULTISET, SEQUENCE  | java.lang.Object[], java primitive type array, java wrapper class array |
    +------------------------+--------------------------+-------------------------------------------------------------------------+
    | Special Types          | JSON                     | X (not supported)                                                       |
    |                        +--------------------------+-------------------------------------------------------------------------+
    |                        | OBJECT, OID              | cubrid.sql.CUBRIDOID <interface>                                        |
    |                        +--------------------------+-------------------------------------------------------------------------+
    |                        | CURSOR                   | java.sql.ResultSet <interface>                                          |
    +------------------------+--------------------------+-------------------------------------------------------------------------+

**묵시적 데이터 타입 변환**

위의 표와 같이 SQL의 데이터 타입과 Java의 데이터 타입이 일치하지 않는 경우, CUBRID는 다음 표에 따라 묵시적으로 데이터 타입 변환을 시도한다.
묵시적 데이터 변환으로 인해 데이터가 손실될 수 있음을 주의해야한다.

    +-------------------------+----------------+-----------------+-------------------+-----------------+-----------------+------------------+----------------------+------------------+---------------+--------------------+
    |                         | **Java Data Types**                                                                                                                                                                        |
    |                         +----------------+-----------------+-------------------+-----------------+-----------------+------------------+----------------------+------------------+---------------+--------------------+
    |                         | byte,          | short,          | int,              | long,           | float,          | double,          |                      |                  |               |                    |
    | **SQL Data Types**      | java.lang.Byte | java.lang.Short | java.lang.Integer | java.lang.Long  | java.lang.Float | java.lang.Double | java.math.BigDecimal | java.lang.String | java.sql.Time | java.sql.Timestamp |
    +=========================+================+=================+===================+=================+=================+==================+======================+==================+===============+====================+
    | **SHORT, SMALLINT**     | O              | O               | O                 | O               | O               | O                | O                    | O                | X             | X                  |
    +-------------------------+----------------+-----------------+-------------------+-----------------+-----------------+------------------+----------------------+------------------+---------------+--------------------+
    | **INT, INTEGER**        | O              | O               | O                 | O               | O               | O                | O                    | O                | X             | X                  |
    +-------------------------+----------------+-----------------+-------------------+-----------------+-----------------+------------------+----------------------+------------------+---------------+--------------------+
    | **BIGINT**              | O              | O               | O                 | O               | O               | O                | O                    | O                | X             | X                  |
    +-------------------------+----------------+-----------------+-------------------+-----------------+-----------------+------------------+----------------------+------------------+---------------+--------------------+
    | **NUMERIC, DECIMAL**    | O              | O               | O                 | O               | O               | O                | O                    | O                | X             | X                  |
    +-------------------------+----------------+-----------------+-------------------+-----------------+-----------------+------------------+----------------------+------------------+---------------+--------------------+
    | **FLOAT, REAL**         | O              | O               | O                 | O               | O               | O                | O                    | O                | X             | X                  |
    +-------------------------+----------------+-----------------+-------------------+-----------------+-----------------+------------------+----------------------+------------------+---------------+--------------------+
    | **DOUBLE**              | O              | O               | O                 | O               | O               | O                | O                    | O                | X             | X                  |
    | **DOUBLE PRECISION**    |                |                 |                   |                 |                 |                  |                      |                  |               |                    |
    +-------------------------+----------------+-----------------+-------------------+-----------------+-----------------+------------------+----------------------+------------------+---------------+--------------------+
    | **DATE**                | X              | X               | X                 | X               | X               | X                | X                    | O                | O             | O                  |
    +-------------------------+                |                 |                   |                 |                 |                  |                      |                  |               |                    |
    | **TIME**                |                |                 |                   |                 |                 |                  |                      |                  |               |                    |
    +-------------------------+                |                 |                   |                 |                 |                  |                      |                  |               |                    |
    | **TIMESTAMP**           |                |                 |                   |                 |                 |                  |                      |                  |               |                    |
    +-------------------------+                |                 |                   |                 |                 |                  |                      |                  |               |                    |
    | **DATETIME**            |                |                 |                   |                 |                 |                  |                      |                  |               |                    |
    +-------------------------+----------------+-----------------+-------------------+-----------------+-----------------+------------------+----------------------+------------------+---------------+--------------------+
    | **CHAR**                | O              | O               | O                 | O               | O               | O                | O                    | O                | O             | O                  |
    +-------------------------+                |                 |                   |                 |                 |                  |                      |                  |               |                    |
    | **VARCHAR**             |                |                 |                   |                 |                 |                  |                      |                  |               |                    |
    +-------------------------+----------------+-----------------+-------------------+-----------------+-----------------+------------------+----------------------+------------------+---------------+--------------------+
    | **SET**                 | X              | X               | X                 | X               | X               | X                | X                    | X                | X             | X                  |
    +-------------------------+                |                 |                   |                 |                 |                  |                      |                  |               |                    |
    | **MULTISET**            |                |                 |                   |                 |                 |                  |                      |                  |               |                    |
    +-------------------------+                |                 |                   |                 |                 |                  |                      |                  |               |                    |
    | **SEQUENCE**            |                |                 |                   |                 |                 |                  |                      |                  |               |                    |
    +-------------------------+----------------+-----------------+-------------------+-----------------+-----------------+------------------+----------------------+------------------+---------------+--------------------+

    - X: 묵시적 변환을 허용하지 않음
    - O: 묵시적 변환 발생