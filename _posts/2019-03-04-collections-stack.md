---
layout: post
title: Stack 구현
categories: JAVA
tags: [Collections]
comments: true
---

그동안 자기 개발에 완전히 소홀했기 때문에 자바 기본기 공부를 위해 <code class="highlight">ArrayList</code>를 이용하여 <code class="highlight">Stack</code>을 구현해보았다.
참고로 자바에서 제공하는 <code class="highlight">java.util.Stack</code>은 <code class="highlight">Vector</code>를 상속받아 구현하였다.

~~~java
import java.util.ArrayList;
import java.util.List;

/**
 * Created by JooWon0712.
 * User: joowon
 * Date: 2019-03-03
 * Time: 21:52
 */
public class Stack {

    private int top;
    private List<Object> stack;
    private int size;

    private Stack(int size) {
       this.top = -1;
       this.stack = new ArrayList<>(size);
       this.size = size;
    }

    // 스택에서 데이터 꺼내기
    // 스택에서 데이터를 꺼낼때 꺼낸 데이터는 remove 메소드를 통해 제거해주었다.
    // 스택에서 꺼낼 데이터가 없을 경우 Exception 처리해야한다.
    // (테스트코드에선 System.out.println 으로 처리함)
    private void pop() {
        if (this.top == -1) {
            System.out.println("Exception : stack no elements");
            return;
        }

        Object object = this.stack.get(this.top);
        System.out.println("== POP ("+object.toString()+") ==");
        this.stack.remove(object);
        this.top = this.stack.size() - 1;
    }

    // 스택에 데이터 입력하기
    // 스택에 사이즈를 초과할 경우 Exception 처리해야한다.
    // (테스트코드에선 System.out.println 으로 처리함)
    private void push(Object object) {
        if (this.size == this.stack.size()) {
            System.out.println("Exception : Stack full");
            return;
        }

        System.out.println("== PUSH ("+object.toString()+") ==");
        this.top = stack.size();
        this.stack.add(object);
    }

    // 스택에서 젤 마지막에 들어있는 데이터 확인하기
    private void peek() {
        System.out.println("== PEEK ("+this.stack.get(this.top)+") ==");
    }

    // 스택에 쌓여있는 데이터 출력하기
    // 젤 마지막 데이터부터 출력
    private void print() {
        System.out.println("## Stack print start");
        for (int i = this.stack.size()-1; i >= 0; i--) {
            System.out.println(this.stack.get(i));
        }
        System.out.println("## Stack print end");
    }

    public static void main(String[] args) {
        Stack stack = new Stack(3);
        stack.push("String");
        stack.push(1);
        stack.push(3.14);
        stack.pop();
        stack.peek(); // 1
        stack.pop();
        stack.pop();
        stack.pop(); // stack no elements
        stack.push(3.14);
        stack.push(14);
        stack.push("Str");
        stack.push("Str1234"); // stack full
        stack.print();
    }
}

~~~
### 결과
![결과]({{ site.baseurl }}/assets/img/collections-stack-result.png)