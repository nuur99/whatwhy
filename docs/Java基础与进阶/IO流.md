### IO流

- 输入流

  - 字节流（InputStream）

    - FileInputStream

      - ```java
        public class TestInputStream {
        
            @Test
            public void testFileInputStream() throws IOException {
                File file = new File("hello.txt");
        
                FileInputStream fis = new FileInputStream(file);
        
                byte[] buf = new byte[3];
                int len;
                while ((len = fis.read(buf)) != -1) {
                    String s = new String(buf, 0, len);
                    System.out.print(s);
                }
        
                fis.close();
            }
        }
        ```

        

  - 字符流（OutputStream）

- 输出流

  - 字节流（Reader）

    - FileReader

      - ```java
        public class TestReader {
            @Test
            public void readerFile() {
                FileReader fr = null;
                try {
                    //实例化file的对象
                    File file = new File("hello.txt");
                    //构造具体的流
                    fr = new FileReader(file);
                    //使用read读流
                    int data = -1;
                    while ((data = fr.read()) != -1) {
                        System.out.print((char)data);
                    }
                } catch (FileNotFoundException e) {
                    e.printStackTrace();
                } catch (IOException e) {
                    e.printStackTrace();
                } finally {
                    try {
                        if (fr != null)
                            fr.close();
                    } catch (IOException e) {
                        e.printStackTrace();
                    }
                }
        
        
            }
             @Test
            public void readerFile2() {
                FileReader fr = null;
                try {
                    //实例化file的对象
                    File file = new File("hello.txt");
                    //构造具体的流
                    fr = new FileReader(file);
                    //使用read读流
                    char[] cbuf = new char[3];
                    int len = -1;
                    while ((len = fr.read(cbuf)) != -1) {
                        String s = new String(cbuf, 0, len);
                        System.out.print(s);
                    }
                } catch (FileNotFoundException e) {
                    e.printStackTrace();
                } catch (IOException e) {
                    e.printStackTrace();
                } finally {
                    try {
                        if (fr != null)
                            fr.close();
                    } catch (IOException e) {
                        e.printStackTrace();
                    }
                }
        
        
            }
        
        }
        ```

  - 字符流（Writer）

    - FileWriter

      - ```Java
        
        public class TestFileWriter {
        
            @Test
            public void testFileWriter() throws IOException {
                File file = new File("h.txt");
                FileWriter fw = new FileWriter(file);
                fw.write("dsdsdsdsdsd");
                fw.close();
            }
        }
        
        ```

        

- 缓冲流
  - BufferInputStream
  - BufferOutputStream
  - BufferReader
  - BufferWriter

