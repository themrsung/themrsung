### Always code like a gigachad :)
    
    var random = new Random();

    var mapper = (DoubleUnaryOperator) x -> x * 10 + 5;
    var provider = (IntSupplier) () -> (int) Math.round(mapper.applyAsDouble(random.nextDouble()));
    var function = (IntBinaryOperator) Integer::sum;

    var result = function.applyAsInt(provider.getAsInt(), provider.getAsInt());

    System.out.println(result);
