---
title: "25일차 guestbook"
excerpt: "평가 후CRUD 프로그램 작성(2)"

categories:
  - study
tags:
  - Java
  - jdbc
  - SQL
  - Oracle
last_modified_at: 2022-09-06T23:00:00-05:00
---
# 25일차

> 방명록 프로그램
> 

> 추가 구현 기술 : 로그인 기능 및 비회원 글 작성 삭제 수정 기능
> 

> 스프링 배우고 security 쓰게되면 두 줄이면 끝납니다.
> 

### 조건

```sql
조건0. 프로그램 컨셉에 따른 데이터 저장 테이블을 만들고, sequence를 꼭 만들어 적용할 것
조건1. MVC 패턴을 적용하며 입력/삭제/수정 기능은 DAO 를 만들어 적용할 것 ( 조회 기능은 아직 안배웠기 때문에 main에 제작 )
조건2. 기본 CRUD 기능 및 검색 기능까지 만들 것
 세부 조건0. 출력 기능은 필요데이터 전부를 출력하되 작성 날짜는 MM월dd일 hh시:mm분 형태로 출력할 것  
 세부 조건1. 삭제와 수정은 글 번호 값을 기반으로 제작
 세부 조건2. 검색 기능은 작성자 이름을 기반으로 제작
 세부 조건3. 수정 기능은 글 번호와 작성날짜를 제외한 데이터를 재 입력받는 기능으로 제작
 세부 조건4. 수정/삭제/검색 기능은 대상 데이터를 찾지 못 할 시, "대상을 찾을 수 없습니다" 출력 하기
```

### DB SQL QUERY

```sql
create table guestbook( // gb List db

    GB_NO NUMBER PRIMARY KEY,
    GB_MEMBER_ID REFERENCES GUESTBOOK_ACCOUNT(GB_MEMBER_ID),
    GB_TITLE VARCHAR2(100),
    GB_CONTENTS VARCHAR2(3000),
    GB_TIME varchar2(30)
    
    
);

CREATE TABLE GUESTBOOK_ACCOUNT( // account db

    GB_MEMBER_NO NUMBER PRIMARY KEY,
    GB_MEMBER_ID VARCHAR2(30) UNIQUE,
    GB_MEMBER_PW VARCHAR2(20),
    GB_MEMBER_NAME VARCHAR2(10)
);

INSERT INTO GUESTBOOK_ACCOUNT // default 비회원값 삽입
VALUES(TB_GBAC_SEQ.nextval, '비회원', 'NONMEMBER', '비회원');

CREATE TABLE GUESTBOOK_NONMEMBER( // 비회원 로그 저장
    
    gb_nonmember_id varchar2(10) default '비회원',
    gb_nonmember_no references guestbook(gb_no),
    gb_nonmember_pw varchar2(20)
);

CREATE SEQUENCE TB_GB_SEQ //gb seq
start with 1001
increment by 1
maxvalue 9999
nocache;

CREATE SEQUENCE TB_GBAC_SEQ // account seq
start with 101
increment by 1
maxvalue 999
nocache;
```

### Main Code

```java
import java.util.ArrayList;
import java.util.Scanner;

public class Main {
	private static boolean islogined = false;

	public static Scanner sc = new Scanner(System.in);

	public static String logined_uid;

	public static int getValidnum() {
		// exception code

		while (true) {
			try {
				int num = Integer.parseInt(sc.nextLine());
				return num;

			} catch (NumberFormatException e) {
				System.out.println("숫자를 입력해주세요.");
			}
		}

	}

	public static void main(String[] args) {

		try { //library 등록
			Class.forName("oracle.jdbc.driver.OracleDriver");
		} catch (ClassNotFoundException e1) {
			e1.printStackTrace();
			System.out.println("에러가 발생했습니다.");
		}
		
		Guestbook_DAO dao = new Guestbook_DAO();
		while (true) { // main interface

			System.out.println("<< 방명록 프로그램 >>");
			System.out.println("1. 방명록 등록");
			System.out.println("2. 방명록 검색");
			System.out.println("3. 방명록 삭제");
			System.out.println("4. 방명록 수정");
			if (islogined == false) { // 비로그인 화면
				System.out.println("5. 회원 등록");
				System.out.println("6. 로그인");
			} else if (islogined == true) { // 로그인 화면
				System.out.println("5. 로그아웃");
			}
			System.out.println("0. 프로그램 종료");
			System.out.print(">> ");
			int menu = getValidnum();

			if (menu == 1) { // insert

				if (islogined == false) { // 비회원용
					System.out.print("비회원용 PW 입력 : ");
					String upw = sc.nextLine();

					try {
						System.out.print("방명록 제목 등록 : ");
						String title = sc.nextLine();
						System.out.print("방명록 내용 등록 : ");
						String contents = sc.nextLine();
						int result = dao.nonmember_writing(upw, title, contents);
						if (result > 0) {
							System.out.println("등록 완료.");
						} else {
							System.out.println("등록 실패");
						}
					} catch (Exception e) {
						e.printStackTrace();
						System.out.println("같은 오류가 반복되면 관리자에게 문의하세요");
					}
				} else if (islogined == true) { // 회원용
					try {

						System.out.print("방명록 제목 등록 : ");
						String title = sc.nextLine();
						System.out.print("방명록 내용 등록 : ");
						String contents = sc.nextLine();
						int result = dao.writing(logined_uid, title, contents);
						if (result > 0) {
							System.out.println("등록 완료.");
						} else {
							System.out.println("등록 실패");
						}
					} catch (Exception e) {
						e.printStackTrace();
						System.out.println("같은 오류가 반복되면 관리자에게 문의하세요");
					}

				}

			} else if (menu == 2) { // search

				ArrayList<Select> tempArray = new ArrayList<Select>();
				System.out.print("작성자 검색 : ");
				String name = sc.nextLine();

				try { 
					tempArray = dao.search(name);
					System.out.println("번호\t작성자\t제목\t\t작성시간\t\t\t내용");
					if (tempArray.size() == 0) {
						System.out.println("데이터를 찾을 수 없습니다.");
					} else {
						for (Select i : tempArray) {
							System.out.println(i.getNo() + "\t" + i.getName() + "\t" + i.getTitle() + "\t" + i.getTime()
									+ "\t" + i.getContents());
						}
					}

				} catch (Exception e) {

					e.printStackTrace();
					System.out.println("같은 에러 반복시 관리자에게 문의하세요.");
				}

			} else if (menu == 3) { // delete

				System.out.print("삭제 할 게시글의 번호를 입력해주세요 : ");
				int gb_no = getValidnum();
				if (islogined == false) {
					System.out.print("USER PW 확인 : ");
					String upw = sc.nextLine();
					String uid = "비회원";

					try {
						int result = dao.delete(islogined, uid, gb_no, upw);
						if (result > 0) {
							System.out.println("삭제 완료.");
						} else {
							System.out.println("삭제 실패.");
						}
					} catch (Exception e) {
						e.printStackTrace();
						System.out.println("같은 오류가 반복되면 관리자에게 문의하세요");
					}

				} else {
					try {
						String upw = ""; 
						// PW가 필요없으나 인자값 때문에 임시로 넣어두었습니다.

						int result = dao.delete(islogined, logined_uid, gb_no, upw);
						if (result > 0) {
							System.out.println("삭제 완료.");
						} else {
							System.out.println("삭제 실패.");
						}
					} catch (Exception e) {
						e.printStackTrace();
						System.out.println("같은 오류가 반복되면 관리자에게 문의하세요");
					}

				}
			}

			else if (menu == 4) { // update

				System.out.print("수정할 글 번호 : ");
				int gb_no = getValidnum();
				if (islogined == false) {
					System.out.print("비회원 설정 PW : ");
					String upw = sc.nextLine();
					try {
						String uid = "비회원";
						System.out.print("수정할 제목 입력 : ");
						String title = sc.nextLine();
						System.out.println("수정할 내용 입력 : ");
						String contents = sc.nextLine();

						int result = dao.update(islogined, uid, gb_no, title, contents, upw);

						if (result > 0) {
							System.out.println("수정 완료");
						} else {
							System.out.println("수정 실패");
						}

					} catch (Exception e) {
						e.printStackTrace();
						System.out.println("같은 오류가 반복되면 문의하세요.");
					}
				}else {
					try {
						System.out.print("수정할 제목 입력 : ");
						String title = sc.nextLine();
						System.out.println("수정할 내용 입력 : ");
						String contents = sc.nextLine();
						String upw = "";

						int result = dao.update(islogined, logined_uid, gb_no, title, contents, upw);

						if (result > 0) {
							System.out.println("수정 완료");
						} else {
							System.out.println("수정 실패");
						}

					} catch (Exception e) {
						e.printStackTrace();
						System.out.println("같은 오류가 반복되면 문의하세요.");
					}
					
				}

			} else if (menu == 5 && islogined == false) {// register

				System.out.print("생성할 아이디를 입력해주세요. : ");
				String id = sc.nextLine();
				System.out.print("비밀번호를 등록해주세요. : ");
				String pw = sc.nextLine();
				System.out.print("사용자 닉네임을 입력해주세요. : ");
				String name = sc.nextLine();
				try {
					int isCreated = dao.createAccount(id, pw, name);
					if (isCreated > 0) {
						System.out.println("생성 완료");

					}
				} catch (Exception e) {
					e.printStackTrace();
					System.out.println("같은 오류가 반복되면 관리자에게 문의하세요");

				}
			} else if (menu == 5 && islogined == true) { // logout

				System.out.print("로그아웃 하시겠습니까? Y, N : ");
				String logout = sc.nextLine();
				if (logout.equals("Y")) {
					islogined = false;
					logined_uid = "";
				} else if (logout.equals("N")) {

				} else {
					System.out.println("잘못된 입력입니다.");
				}

			} else if (menu == 6 && islogined == false) { // login

				System.out.print("ID 입력 : ");
				String ac_id = sc.nextLine();
				System.out.print("PW 입력 : ");
				String ac_pw = sc.nextLine();
				try {
					if (dao.login(ac_id, ac_pw)) {
						islogined = true;
						logined_uid = ac_id;
						System.out.println("로그인 완료");
					} else {
						System.out.println("로그인 실패");
					}
				} catch (Exception e) {
					e.printStackTrace();
					System.out.println("같은 오류가 반복되면 관리자에게 문의하세요");
				}
			}

			else if (menu == 0) {
				System.out.println("프로그램을 종료합니다.");
				System.exit(0);
			} else {
				System.out.println("잘못된 입력입니다.");
			}

		}

	}

}
```

### DAO

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.util.ArrayList;

public class Guestbook_DAO {

	// private ArrayList<Select> selList = new ArrayList<Select>();

	private String dbURL = "jdbc:oracle:thin:@localhost:1521:xe";
	private String dbID = "foodie";
	private String dbPW = "foodie";

	public int createAccount(String ac_id, String ac_pw, String Name) throws Exception {

		Connection con = DriverManager.getConnection(dbURL, dbID, dbPW);
		String sql = "insert into GUESTBOOK_ACCOUNT values(tb_gbac_seq.nextval,?,?,?)";
		PreparedStatement pstat = con.prepareStatement(sql);
		pstat.setString(1, ac_id);
		pstat.setString(2, ac_pw);
		pstat.setString(3, Name);

		int result = pstat.executeUpdate();

		con.commit();
		con.close();
		return result;

	}

	public boolean login(String ac_id, String ac_pw) throws Exception {
		
		Connection con = DriverManager.getConnection(dbURL, dbID, dbPW);

		String sql = "select GB_MEMBER_ID, GB_MEMBER_PW FROM GUESTBOOK_ACCOUNT WHERE GB_MEMBER_ID = ? AND GB_MEMBER_PW = ?";
		PreparedStatement pstat = con.prepareStatement(sql);
		pstat.setString(1, ac_id);
		pstat.setString(2, ac_pw);
		ResultSet rs = pstat.executeQuery();
		boolean islogin = rs.next();
		return islogin;

	}

	public int nonmember_writing(String upw, String title, String contents) throws Exception {
		// 비회원 글 쓰기 기능	
	
		Connection con = DriverManager.getConnection(dbURL, dbID, dbPW);
		String sql = "INSERT INTO GUESTBOOK VALUES(TB_GB_SEQ.nextval, '비회원' , ?, ?, TO_CHAR(SYSDATE, 'RR/MM/DD HH24:MI:SS'))";
		PreparedStatement pstat = con.prepareStatement(sql);
		pstat.setString(1, title);
		pstat.setString(2, contents);

		int result = pstat.executeUpdate();
		sql = "INSERT INTO GUESTBOOK_NONMEMBER VALUES( default, TB_GB_SEQ.currval, ?)";
		pstat = con.prepareStatement(sql);
		pstat.setString(1, upw);

		result = pstat.executeUpdate();
		con.commit();
		con.close();
		return result;

	}

	public int writing(String uid, String title, String contents) throws Exception {
		
		// 회원 글 쓰기
		Connection con = DriverManager.getConnection(dbURL, dbID, dbPW);
		String sql = "INSERT INTO GUESTBOOK VALUES(TB_GB_SEQ.nextval, ? , ?, ?, TO_CHAR(SYSDATE, 'RR/MM/DD HH24:MI:SS'))";

		PreparedStatement pstat = con.prepareStatement(sql);
		pstat.setString(1, uid);
		pstat.setString(2, title);
		pstat.setString(3, contents);

		int result = pstat.executeUpdate();

		con.commit();
		con.close();
		return result;

	}

	public ArrayList<Select> search(String pname) throws Exception {
		
		ArrayList<Select> result = new ArrayList<Select>();
		Connection con = DriverManager.getConnection(dbURL, dbID, dbPW);
		String sql = "SELECT * FROM GUESTBOOK WHERE GB_MEMBER_ID = (select GB_MEMBER_ID FROM GUESTBOOK_ACCOUNT WHERE GB_MEMBER_NAME = ?)";

		PreparedStatement pstat = con.prepareStatement(sql);
		pstat.setString(1, pname);

		ResultSet rs = pstat.executeQuery();
		while (rs.next()) {
			int gb_no = rs.getInt("GB_NO");
			String gb_member_id = rs.getString("GB_MEMBER_ID");
			String gb_title = rs.getString("GB_TITLE");
			String gb_contents = rs.getString("GB_CONTENTS");
			String gb_time = rs.getString("GB_TIME");
			result.add(new Select(gb_no, pname, gb_member_id, gb_title, gb_time, gb_contents));
		}
		con.close();
		return result;
	}

	public int delete(boolean logined, String ac_id, int gb_no, String upw) throws Exception {

		if (logined == true) { // 회원 삭제
			Connection con = DriverManager.getConnection(dbURL, dbID, dbPW);
			String sql = "delete from guestbook where GB_MEMBER_ID = ? AND GB_NO = ?";
			PreparedStatement pstat = con.prepareStatement(sql);
			pstat.setString(1, ac_id);
			pstat.setInt(2, gb_no);

			int result = pstat.executeUpdate();

			con.commit();
			con.close();
			return result;
		} else { // 비회원 삭제
			Connection con = DriverManager.getConnection(dbURL, dbID, dbPW);
			String sql = "delete from guestbook_nonmember where gb_nonmember_no = ? and gb_nonmember_pw = ?";;
			PreparedStatement pstat = con.prepareStatement(sql);
			pstat.setInt(1, gb_no);
			pstat.setString(2, upw);

			int result = pstat.executeUpdate();
			
			
			if (result > 0) {
				String sql2 = "delete from guestbook where gb_no = ?";
				pstat = con.prepareStatement(sql2);
				pstat.setInt(1, gb_no);
				result = pstat.executeUpdate();
				
			}
			con.commit();
			con.close();
			return result;

		}
	}

	public int update(boolean islogined, String uid, int gb_no, String title, String contents, String upw)
			throws Exception {

		Connection con = DriverManager.getConnection(dbURL, dbID, dbPW);
		if (islogined == true) { // 회원 방명록 수정
			String sql = "update guestbook set gb_title = ? ,gb_contents = ? where GB_NO = ? and GB_MEMBER_ID = ?";
			PreparedStatement pstat = con.prepareStatement(sql);
			pstat.setString(1, title);
			pstat.setString(2, contents);
			pstat.setInt(3, gb_no);
			pstat.setString(4, uid);

			int result = pstat.executeUpdate();

			con.commit();
			con.close();
			return result;
		}
		else { // 비회원 방명록 수정
			// 비회원 PW 검사
			String sql = "SELECT * FROM GUESTBOOK_nonmember WHERE GB_nonmember_no = ? and Gb_nonmember_pw = ?";

			PreparedStatement pstat = con.prepareStatement(sql);
			pstat.setInt(1, gb_no);
			pstat.setString(2, upw);
			ResultSet rs = pstat.executeQuery();
			if (rs.next()) { // 수정작업
				String sql2 = "update guestbook set gb_title = ? ,gb_contents = ? where GB_NO = ?";
				pstat = con.prepareStatement(sql2);
				pstat.setString(1, title);
				pstat.setString(2, contents);
				pstat.setInt(3, gb_no);
				
			}
			int result = pstat.executeUpdate();
			if (result > 0)
			con.commit();
			con.close();
			return result;
			
		}

	}

}
```

### Select Class

```java
public class Select { // select 인스턴스 생성
	
	private int no;
	private String name;
	private String id;
	private String title;
	private String contents;
	private String time; // 문자열로 받습니다.
	//private time
	
	
	public Select() {
		super();
	}
	
	public Select(int no, String name, String id, String title, String time, String contents) {
		super();
		this.no = no;
		this.name = name;
		this.id = id;
		this.title = title;
		this.time = time;
		this.contents = contents;
	}
	
	public int getNo() {
		return no;
	}
	
	public String getName() {
		return name;
	}
	
	public String getId() {
		return id;
	}
	
	public String getTitle() {
		return title;
	}
	public String getTime() {
		return time;
	}
	
	public String getContents() {
		return contents;
	}
	

}
```