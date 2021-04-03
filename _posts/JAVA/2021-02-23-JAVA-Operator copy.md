---
layout: post
current: post
cover:  assets/built/images/JAVA.png
navigation: True
title: JAVA 연산자 #
date: 2021-02-23 16:40:00 #
tags: [JAVA] #태그명
class: post-template
subclass: 'post tag-JAVA'
author: qkqwof #author.yml에서 설정한 author
---
간단한 연산자 알아보기




``` Java
//메인 메서드를 가지는 것으로 이름이 저장되고, 메인 메서드를 가지는 것에 public을 붙임
public class OperatorTest1 {

	public static void main(String[] args) {
		int i = 5; //i,j 지역변수 -> 메서드 안에 있으니까!
		int j = 3; //local 변수 사용시 초기화 반드시 해야한다. 얘는 기본값이 없어서 초기화 안하면 바로 에러.
		
		
		Operator o1 = new Operator();//식구가 메모리에 올라간다 --> 주소값할당(라벨링 100)
		Operator o2 = new Operator();//(라벨링 200)
		
		//주소값 어떻게 들어갔는지 확인 ==> 연산자
		// == (same)
		o1 = o2; // =(assign)
		System.out.println(o1);
		System.out.println(o2);
		System.out.println(o1==o2);//false
		
		// %...mod -> 나누었을 때 나머지
		System.out.println("연산자 % :: " + i%j);//2
		System.out.println(i==j);//false
		System.out.println(i!=j);//같지 않다 -> true
		
		// ++(1씩 증가) , --(1씩 감소)
		int k = 10;
		System.out.println(k++);//10...1이 나중에 증가
		System.out.println(k++);//11
		
		int m = 10;
		System.out.println(++m);//11...1이 먼저 증가
		System.out.println(m);//11
		//--도 똑같은 원리로 돌아감.
		
		//논리 연산자
		System.out.println(o1.test1() | o1.test2());//true
		System.out.println("##################################");
		System.out.println(o1.test2() & o1.test1());//false
		
		System.out.println("===================================");
		
		//Short Circuit
		System.out.println(o1.test1() || o1.test2());//true
		System.out.println("##################################");
		System.out.println(o1.test2() && o1.test1());//false
		//예외 상황시 건너뛸 수 있음
		
		
	}//main
}//class

class Operator{
	public boolean test1() {
		System.out.println("test1() Calling....");
		return true;
	}
	
	public boolean test2() {
		System.out.println("test2() Calling....");
		return false;
	}
}
```