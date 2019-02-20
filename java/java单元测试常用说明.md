# <center>java单元测试常用</center>
* 基本验证输出 Assert.assertEquals(testMethod(),actrueRet)
* 验证是否可以抛出预期的异常：    加注解@Test(expected = InputException.class)
* 当被测试方法中含有Scanner时，使用：  
```
	//输出值测试
    @Test
    public void testOutputData() throws InputException {
        YangHuiTriangle yangHuiTriangle = new YangHuiTriangle();
        String inputData = "7 5";
        InputStream stdin = System.in;
        try {
            System.setIn(new ByteArrayInputStream(inputData.getBytes()));
            int data = yangHuiTriangle.outputData();
            System.out.println("根据输入行列获取到的数据为："+data);
        } finally {
            System.setIn(stdin);
        }
    } //被测代码public int outputData() throws InputException {
        Scanner input = new Scanner(System.in);
        int m = input.nextInt();
        int n = input.nextInt();
        return getYHData(m, n);
    }
 ```  
  
 [测试方法有输入操作时参考链接](https://stackoverflow.com/questions/3550339/how-testjunit-input-keyboard-in-java)
    
