    @FunctionalInterface
    interface Adder {
        int compute(int x, int y);
    }
    
    class NormalPerson implements Adder {
        @Override
        public int compute(int x, int y) {
            return x + y;
        }
    }
    
    class Programmer implements Adder {
        @Override
        public int compute(int x, int y) {
            if ((Integer) x == null || (Integer) y == null) throw new NullPointerException("nope");
            
            long lx = x;
            long ly = y;
            
            if (lx + ly > Integer.MAX_VALUE) throw new IllegalArgumentException("nope");
            
            return x + y;
        }
    }
    
    class Me implements Adder {
        @Override
        public int compute(int x, int y) {
            return IntStream.of(x, y).reduce(0, (a, b) -> a + b);
        }
    }
