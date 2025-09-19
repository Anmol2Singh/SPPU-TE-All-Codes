create table O_RollCall_Anmol(name varchar(10), roll int, attendance int);
create table N_RollCall_Anmol(name varchar(10), roll int, attendance int);

INSERT INTO O_RollCall_Anmol values ('Anmol', 7, 99);
INSERT INTO O_RollCall_Anmol values ('Adinath', 9, 75);
INSERT INTO O_RollCall_Anmol values ('Raj', 13, 89);
INSERT INTO O_RollCall_Anmol values ('Aditya', 12, 95);
INSERT INTO O_RollCall_Anmol values ('Darshan', 11, 92);

INSERT INTO N_RollCall_Anmol values ('Adinath', 9, 75);
INSERT INTO N_RollCall_Anmol values ('Ram', 72, 62);
INSERT INTO N_RollCall_Anmol values ('Pranav', 49, 74);
INSERT INTO N_RollCall_Anmol values ('Aditya', 12, 95);
INSERT INTO N_RollCall_Anmol values ('Darshan', 11, 92);

SET SERVEROUTPUT ON

DECLARE
cursor c_roll (p_roll IN Number) IS 
select 1 from O_RollCall_Anmol where roll = p_roll;

v_roll N_RollCall_Anmol.roll%type;
v_name N_RollCall_Anmol.name%type;
v_attendance N_RollCall_Anmol.attendance%type;

v_dummy number;

Cursor cnew IS 
select roll, name, attendance from N_RollCall_Anmol;

BEGIN
open cnew;

loop
fetch cnew into v_roll, v_name, v_attendance;
exit when cnew%notfound;

open c_roll(v_roll);
fetch c_roll into v_dummy;

IF c_roll%notfound then
insert into O_RollCall_Anmol (roll, name, attendance)
values (v_roll, v_name, v_attendance);
dbms_output.put_line('Inserted new record from Roll ID: ' || v_roll);

else
dbms_output.put_line('Record already exists for Roll ID: ' || v_roll || '. Skipping.');
end if;

close c_roll;

end loop;

close cnew;

commit;

dbms_output.put_line('Merge operation completed successfully.');

exception
when others then
dbms_output.put_line('An error occurred: ' || SQLERRM);
rollback;
end;
/

select * from O_RollCall_Anmol;
