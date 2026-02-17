Первое задание<br/>
  SELECT CONCAT( rel.familyName, ' ', rel.givenName, ' ', rel.middleName) AS [ФИО] ,rel.birthDate AS [Дата рождения] ,dep.contactRelationship AS [Родственная связь]<br/>
FROM HPPersonGeneric AS emp<br/>
JOIN HPPersonDependant AS dep <br/>
    ON emp.sysId = dep.HPPersonGenericSysId<br/>
JOIN HPPersonGeneric AS rel <br/>
    ON dep.HPRelatedPersonSysId = rel.sysId<br/>
WHERE emp.personId = 'test';<br/>

Задача вторая <br/>


    public Date getVacCheck(Date date) {
        Calendar cal = Calendar.getInstance();
        cal.setTime(date);
        
        int dayOfWeek = cal.get(Calendar.DAY_OF_WEEK);
        
        if (dayOfWeek == Calendar.SATURDAY) {
        } else if (dayOfWeek == Calendar.SUNDAY) {
            cal.add(Calendar.DAY_OF_MONTH, 1); 
        }
        
        return new Date(cal.getTimeInMillis());
    }

    public Date getNextShippingDate() {
        Calendar now = Calendar.getInstance();
        int[] sendDays = {1, 10, 20};
        for (int day : sendDays) {
            Calendar target = Calendar.getInstance();
            target.set(Calendar.DAY_OF_MONTH, day);
            target.set(Calendar.HOUR_OF_DAY, 18);
            target.set(Calendar.MINUTE, 0);
            target.set(Calendar.SECOND, 0);
            target.set(Calendar.MILLISECOND, 0);
            
            
            if (target.before(now)) {
                target.add(Calendar.MONTH, 1);
            }

            Date targetDate = new Date(target.getTimeInMillis());
            Date validDate = getVacCheck(targetDate);

            Calendar validCal = Calendar.getInstance();
            validCal.setTime(validDate);

            if (!validCal.before(now)) {
                return validDate;
            }
        }

        Calendar nextMonth = Calendar.getInstance();
        nextMonth.add(Calendar.MONTH, 1);
        nextMonth.set(Calendar.DAY_OF_MONTH, 1);
        nextMonth.set(Calendar.HOUR_OF_DAY, 18);
        nextMonth.set(Calendar.MINUTE, 0);
        nextMonth.set(Calendar.SECOND, 0);
        
        return getVacCheck(new Date(nextMonth.getTimeInMillis()));
    }
Задание третье:

    // Массивы для преобразования чисел в слова
    private static final String[] UNITS = {
        "", "один", "два", "три", "четыре", "пять", "шесть", "семь", "восемь", "девять",
        "десять", "одиннадцать", "двенадцать", "тринадцать", "четырнадцать", "пятнадцать",
        "шестнадцать", "семнадцать", "восемнадцать", "девятнадцать"
    };
    
    private static final String[] TENS = {
        "", "", "двадцать", "тридцать", "сорок", "пятьдесят", 
        "шестьдесят", "семьдесят", "восемьдесят", "девяносто"
    };
    
    private static final String[] HUNDREDS = {
        "", "сто", "двести", "триста", "четыреста", "пятьсот",
        "шестьсот", "семьсот", "восемьсот", "девятьсот"
    };
    
    private static final String[] THOUSANDS = {
        "", "тысяча", "тысячи", "тысяч"
    };
    
    private static final String[] CURRENCY_MAIN = {
        "рубль", "рубля", "рублей"
    };
    
    private static final String[] CURRENCY_FRACTION = {
        "копейка", "копейки", "копеек"
    };

    /**
     * Преобразует сумму в прописное написание
     * @param amount сумма (до 99 999.99 включительно)
     * @return строка с прописным написанием суммы
     */
    public static String amountToWords(BigDecimal amount) {
        if (amount == null) {
            throw new IllegalArgumentException("Сумма не может быть null");
        }
        
        // Проверяем диапазон (до 99 999.99)
        if (amount.compareTo(new BigDecimal("99999.99")) > 0) {
            throw new IllegalArgumentException("Сумма превышает допустимый лимит 99 999.99");
        }
        
        if (amount.compareTo(BigDecimal.ZERO) < 0) {
            throw new IllegalArgumentException("Сумма не может быть отрицательной");
        }
        
        // Округляем до 2 знаков после запятой
        amount = amount.setScale(2, RoundingMode.HALF_UP);
        
        // Разделяем на целую и дробную части
        long rubles = amount.longValue();
        int kopecks = amount.remainder(BigDecimal.ONE)
                           .multiply(new BigDecimal("100"))
                           .intValue();
        
        // Формируем результат
        StringBuilder result = new StringBuilder();
        
        // Обрабатываем рубли
        if (rubles == 0) {
            result.append("ноль ");
        } else {
            result.append(convertNumberToWords(rubles)).append(" ");
        }
        
        // Добавляем правильную форму слова "рубль"
        result.append(getCorrectForm(CURRENCY_MAIN, rubles));
        
        // Добавляем копейки
        result.append(" ").append(String.format("%02d", kopecks)).append(" ");
        result.append(getCorrectForm(CURRENCY_FRACTION, kopecks));
        
        return result.toString().trim();
    }
    
    /**
     * Преобразует число в слова
     */
    private static String convertNumberToWords(long number) {
        if (number == 0) {
            return "ноль";
        }
        
        StringBuilder words = new StringBuilder();
        
        // Тысячи
        if (number >= 1000) {
            int thousands = (int) (number / 1000);
            words.append(convertHundreds(thousands, true)).append(" ");
            words.append(getCorrectForm(THOUSANDS, thousands));
            number %= 1000;
            
            if (number > 0) {
                words.append(" ");
            }
        }
        
        // Сотни, десятки и единицы
        if (number > 0) {
            words.append(convertHundreds((int) number, false));
        }
        
        return words.toString().trim();
    }
    
    /**
     * Преобразует трехзначное число в слова
     * @param number число от 1 до 999
     * @param isThousands true если это тысячи
     */
    private static String convertHundreds(int number, boolean isThousands) {
        if (number == 0) {
            return "";
        }
        StringBuilder result = new StringBuilder();
        
        // Сотни
        int hundreds = number / 100;
        if (hundreds > 0) {
            result.append(HUNDREDS[hundreds]).append(" ");
        }
        
        // Десятки и единицы
        int remainder = number % 100;
        if (remainder < 20) {
            if (remainder > 0) {
                String word = UNITS[remainder];
                if (isThousands) {
                    word = convertThousandsUnits(word);
                }
                result.append(word);
            }
        } else {
            int tens = remainder / 10;
            int units = remainder % 10;
            
            result.append(TENS[tens]);
            
            if (units > 0) {
                String word = UNITS[units];
                if (isThousands) {
                    word = convertThousandsUnits(word);
                }
                result.append(" ").append(word);
            }
        }
        
        return result.toString().trim();
    }
    
    /**
     * Склоняет единицы для тысяч
     */
    private static String convertThousandsUnits(String unit) {
        return switch (unit) {
            case "один" -> "одна";
            case "два" -> "две";
            default -> unit;
        };
    }
    
    /**
     * Возвращает правильную форму слова в зависимости от числа
     */
    private static String getCorrectForm(String[] forms, long number) {
        long lastTwo = number % 100;
        long lastOne = number % 10;
        
        if (lastTwo >= 11 && lastTwo <= 19) {
            return forms[2]; // множественное число (11-19)
        }
        
        return switch ((int) lastOne) {
            case 1 -> forms[0]; // 1
            case 2, 3, 4 -> forms[1]; // 2,3,4
            default -> forms[2]; // 0,5-9
        };
    }

    // Пример использования
    public static void main(String[] args) {
        BigDecimal[] testAmounts = {
            new BigDecimal("12345.67"),
            new BigDecimal("99999.99"),
            new BigDecimal("1000.50"),
            new BigDecimal("215.30"),
            new BigDecimal("1.01"),
            new BigDecimal("0.99"),
            new BigDecimal("0"),
            new BigDecimal("42")
        };
        
        for (BigDecimal amount : testAmounts) {
            try {
                System.out.println(amount + " -> " + amountToWords(amount));
            } catch (Exception e) {
                System.out.println(amount + " -> Ошибка: " + e.getMessage());
            }
        }
    }

