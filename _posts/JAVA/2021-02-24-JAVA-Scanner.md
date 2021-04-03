---
layout: post
current: post
cover:  assets/built/images/JAVA.png
navigation: True
title: JAVA Scanner #
date: 2021-02-24 16:40:00 #
tags: [JAVA] #태그명
class: post-template
subclass: 'post tag-JAVA'
author: qkqwof #author.yml에서 설정한 author
---
실행 시(컴파일 시가 아님) 키보드로 입력된 값을 입력받도록 해주는 기능을 가지고 있는 클래스




``` Java
import java.util.Scanner;

/*
 * Scanner 클래스는
 * 실행 시(컴파일 시가 아님) 키보드로 입력된 값을 입력받도록 해주는 기능을 가지고 있는 클래스 
 */

public class ScannerTest2 {
	public static void main(String[] args) {
		//객체 생성
		System.out.println("키보드로 숫자 2개와 이름을 입려하세요>>");
		Scanner sc = new Scanner(System.in); // System.in -> 키보드 입력값 	
		int i = sc.nextInt(); //공백을 기준으로 데이터를 반환한다.
		int j = sc.nextInt();
		//String name = sc.nextLine();
		sc.nextLine();  // 한 라인을 기준으로 데이터를 반환
		String name = sc.next(); // 공백을 기준으로 데이터를 반환
		
		System.out.println("숫자값 i: "+i+",j: "+j);
		System.out.println(name);
		
	}

}
```