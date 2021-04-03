---
layout: post
current: post
cover:  assets/built/images/JAVA.png
navigation: True
title: JAVA 캡슐화 #
date: 2021-02-25 16:40:00 #
tags: [JAVA] #태그명
class: post-template
subclass: 'post tag-JAVA'
author: qkqwof #author.yml에서 설정한 author
---
외부에서 어떤 클래스의 필드에 접근하지 못하게 하는 방법

캡슐화 방법
1. private 지정
2. setter, getter는 public으로 열어 놓는다.
3. 필드 초기화 되전 직전에 타당한 값만 받을 수 있도록 로직을 제어

``` Java
public class MyDateTest {

	public static void main(String[] args) {
        MyDate md = new MyDate();
        md.setMonth(13);
	md.setDay(33);
```
MyDate 클래스에서 필드에 직접적으로 접근하면 이상한 데이터를 넣을 가능성이 있다.
ex) 13월 또는 33일...
``` Java
public class MyDate {
	//private 지정하면 같은 클래스에서만 접근을 허용
	private int month;// setMonth가 동작할 때,1~12 사이의 값 할당
	private int day;
	
	/*public MyDate(int month, int day) {
		this.month = month;
		this.day = day;
	}*/
	
	public int getMonth() {
		return month;
	}
	public void setMonth(int month) {
		//2. 제어문 1~12월에 해당하는 값만 처리...나머지 메시지...
		if(month>=1 && month<=12) {
			this.month = month;
		} else { //1~12월의 값이 아닌 경우는...
			System.out.println("해당 월이 아닙니다.");
			//return;//다시 월을 입력하도록 함.
			//month가 잘 못 되었는데 day로 넘어갈 이유가 없음
			//프로그램을 빠져나온다.
			System.exit(0);
		}
		
	}
```
따라서 메서드를 통해서 setMonth메서드에서 1~12 이외의 수를 필드에 안 받게 할 수 있다.