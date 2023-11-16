    public static void main(String[] args) {
        // What is the output of this program?
        var squareRoot = (UnaryOperator<DoubleProvider>) (fx) -> (() -> Math.sqrt(fx.getAsDouble()));
        double result = squareRoot.apply(() -> 3).getAsDouble();
        Object object = (Double) result;
        
        System.out.println(squareRoot.toString());
    }
