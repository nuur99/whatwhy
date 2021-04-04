### Java比较器

- 自然排序

  - Comparable

    - ```java
       @Test
          public void testAble() {
              String[] arr = new String[]{"a", "f", "s","n", "g", "f"};
              Arrays.sort(arr);
              for (String s : arr) {
                  System.out.print(s);
              }
          }
      ```

    - ```java
      public final class String
          implements java.io.Serializable, Comparable<String>, CharSequence {}
      ```

    - 实现了Comparable<String>接口

- 定制排序

  - Comparator

  - ```java
    public void testAble() {
            String[] arr = new String[]{"a", "f", "s","n", "g", "f"};
            Arrays.sort(arr, new Comparator<String>() {
                @Override
                public int compare(String o1, String o2) {
                    return -o1.compareTo(o2);
                }
            });
            for (String s : arr) {
                System.out.print(s);
            }
        }
    ```

    