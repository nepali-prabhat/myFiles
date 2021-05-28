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

```
// Copyright (c) 2015, Google Inc. Please see the AUTHORS file for details.
// All rights reserved. Use of this source code is governed by a BSD-style
// license that can be found in the LICENSE file.

library serializers;

import 'package:abstractiontestfl/models/article.dart';
import 'package:abstractiontestfl/models/user.dart';
import 'package:built_value/standard_json_plugin.dart';
import 'package:built_value/serializer.dart';
import 'package:built_collection/built_collection.dart';


part 'serializers.g.dart';

/// Example of how to use built_value serialization.
///
/// Declare a top level [Serializers] field called serializers. Annotate it
/// with [SerializersFor] and provide a `const` `List` of types you want to
/// be serializable.
///
/// The built_value code generator will provide the implementation. It will
/// contain serializers for all the types asked for explicitly plus all the
/// types needed transitively via fields.
///
/// You usually only need to do this once per project.
@SerializersFor([
  Article,
  User,
])

final Serializers serializers =
(_$serializers.toBuilder()
  ..addPlugin(StandardJsonPlugin())).build();
  ```
