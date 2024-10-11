### Always code like a gigachad :)
    
    var random = new Random();

    var mapper = (DoubleUnaryOperator) x -> x * 10 + 5;
    var provider = (IntSupplier) () -> (int) Math.round(mapper.applyAsDouble(random.nextGaussian()));
    var function = (IntBinaryOperator) Integer::sum;

    var result = function.applyAsInt(provider.getAsInt(), provider.getAsInt());

    System.out.println(result);

How many instances were created throughout the lifecycle* of this program?

*assuming this program is ran inside a standard main method, and terminates as soon as the last line has finished
