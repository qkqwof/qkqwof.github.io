---
layout: post
current: post
cover:  assets/built/images/JAVA.png
navigation: True
title: JAVA 생성자 #
date: 2021-02-23 16:40:00 #
tags: [JAVA] #태그명
class: post-template
subclass: 'post tag-JAVA'
author: qkqwof #author.yml에서 설정한 author
---
### 자바 생성자

클래스 작성시
1. 필드선언
2. 값의 주입을 어떻게?
3. 필드 값을 받아오기...

**주목해야 되는 점은 값을 어떻게 필드에 주입, 저장하는가?**
1. Setter로 주입
2. 생성자로 주입




**프로그래머 클래스** 
```Java
import com.encore.util.*;

public class Programmer {
	//필드 생성
	public String name;
	public MyDate birthday;
	public float salary;
	public int bonus;
	
	//클래스를 만들 때, 제일 중요한 것은 필드(멤버변수)선언
	
	//2. 값을 어떻게 필드에 주입, 할당, 저장?
	/*
	 * public void setProgrammer(String name, MyDate birthday, float salary, int
	 * bonus) { this.name = name; this.birthday = birthday; this.salary = salary;
	 * this.bonus = bonus; }
	 */
	
	//생성자에게 추가
	public Programmer(String name, MyDate birthday, float salary, int bonus) {
		this.name = name;
		this.birthday = birthday;
		this.salary = salary;
		this.bonus = bonus;
	}
```
<br>

**실행파일**
```Java
import com.encore.cons.Programmer;
import com.encore.util.MyDate;

public class ProgrammerTest {

	public static void main(String[] args) {
		//1.객체 생성   ---> 식구들을 메모리에 올린다.
		/*
		 * Programmer pro = new Programmer(); MyDate md = new MyDate(); //2.메서드 호출
		 * md.setDate(1980, 12, 7); pro.setProgrammer("박재영", md, 10000.0f, 1000); //3.
		 * 개발자의 정보가 콘솔창에 출력되도록 작성 System.out.println(pro.getProgrammer());
		 */
		Programmer pro = new Programmer("James",new MyDate(1980,9,18),1000.0f,100);
		
		//step2.생성자로 객체 만들기
		System.out.println(pro.getProgrammerInfo());
	}

}
```
맨 뒤의 ;의 의미
1. statement 종결
2. calling

calling을 하면 worker(){} 가 동작하면서 출력된다.
기본 생성자는 생략되어 있는 상태다.

생성자는 객체가 생성될때마다 구동(호출)된다. 객체가 8개 생성되었다면 생성자는 8번 구동된 것이다.

기본 생성자 이외 생성자를 만들면 생략된 기본 생성자를 사용할 수 없고 기본 생성자를 만들어야 한다. 