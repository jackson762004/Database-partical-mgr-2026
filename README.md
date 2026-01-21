1st program 
CREATE TABLE train_details(
  train_name VARCHAR2(20) PRIMARY KEY,
  total_seats NUMBER,
  reserved_seats NUMBER
);

CREATE TABLE reservation_status(
  train_name VARCHAR2(20),
  seat_id NUMBER,
  reserved CHAR(1),
  passenger_name VARCHAR2(20)
);

CREATE TABLE waiting_list(
  slno NUMBER,
  passenger_name VARCHAR2(20),
  train_name VARCHAR2(20)
);



DECLARE
  cname VARCHAR2(20);
  tname VARCHAR2(20);
  tot NUMBER;
  resv NUMBER;
  sid NUMBER;
BEGIN
  cname := '&Passenger_Name';
  tname := '&Train_Name';

  SELECT total_seats, reserved_seats
  INTO tot, resv
  FROM train_details
  WHERE train_name = tname;

  IF tot > resv THEN
    SELECT MIN(seat_id)
    INTO sid
    FROM reservation_status
    WHERE train_name = tname AND reserved = 'N';

    UPDATE reservation_status
    SET reserved = 'Y', passenger_name = cname
    WHERE train_name = tname AND seat_id = sid;

    UPDATE train_details
    SET reserved_seats = reserved_seats + 1
    WHERE train_name = tname;
  ELSE
    INSERT INTO waiting_list
    VALUES (1, cname, tname);
  END IF;
END;
/


2nd program 

CREATE TABLE ACCTMASTER(
  accno NUMBER PRIMARY KEY,
  name VARCHAR2(25),
  balance NUMBER(10)
);

CREATE TABLE ACCTTRAN(
  accno NUMBER,
  tran_date DATE DEFAULT SYSDATE,
  deb_cred VARCHAR2(10),
  flag CHAR(1) DEFAULT 'N',
  amount NUMBER(10)
);

DECLARE
  v_accno  ACCTTRAN.accno%TYPE;
  v_date   ACCTTRAN.tran_date%TYPE;
  v_type   ACCTTRAN.deb_cred%TYPE;
  v_flag   ACCTTRAN.flag%TYPE;
  v_amt    ACCTTRAN.amount%TYPE;

  CURSOR c1 IS
    SELECT accno, tran_date, deb_cred, flag, amount
    FROM ACCTTRAN
    WHERE flag = 'N';
BEGIN
  OPEN c1;
  LOOP
    FETCH c1 INTO v_accno, v_date, v_type, v_flag, v_amt;
    EXIT WHEN c1%NOTFOUND;

    IF v_type = 'Debit' THEN
      UPDATE ACCTMASTER
      SET balance = balance - v_amt
      WHERE accno = v_accno;

    ELSIF v_type = 'Credit' THEN
      UPDATE ACCTMASTER
      SET balance = balance + v_amt
      WHERE accno = v_accno;
    END IF;

    UPDATE ACCTTRAN
    SET flag = 'Y'
    WHERE accno = v_accno AND flag = 'N';
  END LOOP;

  COMMIT;
  CLOSE c1;
END;
/

4th program 

CREATE TABLE marklist(
  regno NUMBER PRIMARY KEY,
  m1 NUMBER,
  m2 NUMBER,
  m3 NUMBER,
  m4 NUMBER,
  m5 NUMBER,
  m6 NUMBER
);

CREATE TABLE result(
  regno NUMBER,
  percentage NUMBER(6,2),
  result VARCHAR2(20),
  arrears NUMBER
);

DECLARE
  rno NUMBER;
  m1 NUMBER;
  m2 NUMBER;
  m3 NUMBER;
  m4 NUMBER;
  m5 NUMBER;
  m6 NUMBER;
  per NUMBER(6,2);
  arr NUMBER := 0;
  res VARCHAR2(20);
BEGIN
  rno := &regno;
  m1 := &m1;
  m2 := &m2;
  m3 := &m3;
  m4 := &m4;
  m5 := &m5;
  m6 := &m6;

  IF m1 < 50 THEN arr := arr + 1; END IF;
  IF m2 < 50 THEN arr := arr + 1; END IF;
  IF m3 < 50 THEN arr := arr + 1; END IF;
  IF m4 < 50 THEN arr := arr + 1; END IF;
  IF m5 < 50 THEN arr := arr + 1; END IF;
  IF m6 < 50 THEN arr := arr + 1; END IF;

  per := (m1+m2+m3+m4+m5+m6)/6;

  IF arr > 0 THEN
    res := 'FAIL';
  ELSIF per >= 75 THEN
    res := 'DISTINCTION';
  ELSIF per >= 60 THEN
    res := 'FIRST CLASS';
  ELSE
    res := 'SECOND CLASS';
  END IF;

  INSERT INTO marklist VALUES(rno,m1,m2,m3,m4,m5,m6);
  INSERT INTO result VALUES(rno,per,res,arr);
END;
/

5th program 

CREATE TABLE lib(
  bookname VARCHAR2(15),
  author VARCHAR2(15),
  publication VARCHAR2(15),
  noofcopies NUMBER
);

CREATE TABLE studentlist(
  rollno NUMBER,
  name VARCHAR2(20),
  no_card NUMBER
);

CREATE TABLE books(
  bookno NUMBER,
  bookname VARCHAR2(15),
  available VARCHAR2(5),
  subscribed_to NUMBER
);

CREATE TABLE subscription(
  bookno NUMBER,
  rollno NUMBER,
  do_sub DATE,
  do_return DATE,
  fineamount NUMBER,
  status VARCHAR2(10)
);

BEGIN
  INSERT INTO studentlist VALUES(&rollno,'&name',2);
END;
/
DECLARE
  bno NUMBER;
  bname VARCHAR2(15);
  auth VARCHAR2(15);
  pub VARCHAR2(15);
  noc NUMBER;
  cnt NUMBER;
BEGIN
  bno := &bookno;
  bname := '&bookname';
  auth := '&author';
  pub := '&publication';
  noc := &noofcopies;

  SELECT COUNT(*) INTO cnt FROM lib WHERE bookname = bname;

  IF cnt = 0 THEN
    INSERT INTO lib VALUES(bname, auth, pub, noc);
  ELSE
    UPDATE lib SET noofcopies = noofcopies + noc WHERE bookname = bname;
  END IF;

  WHILE noc > 0 LOOP
    INSERT INTO books VALUES(bno, bname, 'YES', 0);
    bno := bno + 1;
    noc := noc - 1;
  END LOOP;
END;
/
