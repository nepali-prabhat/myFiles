```
library $1$;
import 'package:built_value/built_value.dart';
import 'package:built_value/serializer.dart';
import 'package:json_annotation/json_annotation.dart';
import 'package:abstractiontestfl/serializers.dart';
part '$1$.g.dart';

@JsonSerializable()
abstract class $CLASS_NAME$ implements Built<$CLASS_NAME$, $CLASS_NAME$Builder> {
    $CLASS_NAME$._();
    factory $CLASS_NAME$([void Function($CLASS_NAME$Builder) updates]) = _$$$CLASS_NAME$;
    
    static Serializer<$CLASS_NAME$> get serializer => _$$$1$Serializer;
    
    factory $CLASS_NAME$.fromJson(Map<String, dynamic> json) {
        $CLASS_NAME$? $1$ = serializers.deserializeWith($CLASS_NAME$.serializer, json);
        return $1$!;
    }

    Map<String, dynamic> toJson() {
        final $1$Json = serializers.serializeWith($CLASS_NAME$.serializer, this);
        return $1$Json as Map<String, dynamic>;
    }
  
}
```
