### Always code like a gigachad :)
    
    @FunctionalInterface
    interface Adder {
        int compute(int x, int y);
    }

    var human = (Adder) (x, y) -> x + y;
    var programmer = (Adder) (x, y) -> {
        if ((Integer) x == null || (Integer) y == null) throw new NullPointerException("nope");
        
        long lx = x;
        long ly = y;
        
        if (lx + ly > Integer.MAX_VALUE) throw new IllegalArgumentException("nope");
        
        return x + y;
    };

    var me = IntStream.of(x, y).reduce(0, (a, b) -> a + b);
