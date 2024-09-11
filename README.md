-- 创建 filter 表 (筛选器)
CREATE TABLE filter (
    id INTEGER PRIMARY KEY, -- 筛选器id
    name TEXT NOT NULL, -- 筛选器名称
    desc TEXT -- 筛选器描述
);

-- 创建 supportLibrary 表 (支持库)
CREATE TABLE supportLibrary (
    id INTEGER PRIMARY KEY, -- 支持库id
    name TEXT NOT NULL, -- 支持库名称
    desc TEXT, -- 支持库描述
    filterId INTEGER, -- 筛选器id，外键
    headerId INTEGER, -- 头文件id，外键
    FOREIGN KEY (filterId) REFERENCES filter(id),
    FOREIGN KEY (headerId) REFERENCES header(id)
);

-- 创建 header 表
CREATE TABLE header (
    id INTEGER PRIMARY KEY,
    name TEXT NOT NULL,
    desc TEXT
);

-- 创建 namespace 表
CREATE TABLE namespace (
    id INTEGER PRIMARY KEY,
    name TEXT NOT NULL,
    headerId INTEGER,
    desc TEXT,
    FOREIGN KEY (headerId) REFERENCES header(id)
);

-- 创建 class 表
CREATE TABLE class (
    id INTEGER PRIMARY KEY,
    name TEXT NOT NULL,
    interface BOOLEAN DEFAULT 0,
    templateClass BOOLEAN DEFAULT 0,
    final BOOLEAN DEFAULT 0,
    headerId INTEGER,
    namespaceId INTEGER,
    baseClassId INTEGER,
    desc TEXT,
    FOREIGN KEY (headerId) REFERENCES header(id),
    FOREIGN KEY (namespaceId) REFERENCES namespace(id),
    FOREIGN KEY (baseClassId) REFERENCES class(id)
);

-- 创建 templateClassParameter 表
CREATE TABLE templateClassParameter (
    id INTEGER PRIMARY KEY,
    name TEXT NOT NULL,
    desc TEXT,
    type TEXT,
    classId INTEGER,
    nonType BOOLEAN DEFAULT 0 CHECK (nonType IN (0, 1)),
    FOREIGN KEY (classId) REFERENCES class(id)
);

-- 创建 constructor 表
CREATE TABLE constructor (
    id INTEGER PRIMARY KEY,
    name TEXT NOT NULL,
    explicit BOOLEAN DEFAULT 0,
    classId INTEGER,
    FOREIGN KEY (classId) REFERENCES class(id)
);

-- 创建 destructor 表
CREATE TABLE destructor (
    id INTEGER PRIMARY KEY,
    name TEXT NOT NULL,
    classId INTEGER,
    FOREIGN KEY (classId) REFERENCES class(id)
);

-- 创建 operators 表
CREATE TABLE operators (
    id INTEGER PRIMARY KEY,
    name TEXT NOT NULL,
    desc TEXT,
    classId INTEGER,
    FOREIGN KEY (classId) REFERENCES class(id)
);

-- 创建 method 表
CREATE TABLE method (
    id INTEGER PRIMARY KEY,
    name TEXT NOT NULL,
    returnType TEXT,
    returnDesc TEXT,
    constReturn BOOLEAN DEFAULT 0,
    const BOOLEAN DEFAULT 0,
    Virtual BOOLEAN DEFAULT 0,
    pureVirtual BOOLEAN DEFAULT 0,
    final BOOLEAN DEFAULT 0,
    inline BOOLEAN DEFAULT 0,
    noexcept BOOLEAN DEFAULT 0,
    constexpr BOOLEAN DEFAULT 0,
    static BOOLEAN DEFAULT 0,
    friend BOOLEAN DEFAULT 0,
    templateFunc BOOLEAN DEFAULT 0,
    classId INTEGER,
    desc TEXT,
    FOREIGN KEY (classId) REFERENCES class(id)
);

-- 创建 methodOverload 表
CREATE TABLE methodOverload (
    id INTEGER PRIMARY KEY,
    desc TEXT,
    constructorId INTEGER,
    destructorId INTEGER,
    methodId INTEGER,
    FOREIGN KEY (constructorId) REFERENCES constructor(id),
    FOREIGN KEY (destructorId) REFERENCES destructor(id),
    FOREIGN KEY (methodId) REFERENCES method(id)
);

-- 创建 methodOverloadParameter 表
CREATE TABLE methodOverloadParameter (
    id INTEGER PRIMARY KEY,
    name TEXT NOT NULL,
    type TEXT NOT NULL,
    const BOOLEAN DEFAULT 0,
    pointer BOOLEAN DEFAULT 0,
    reference BOOLEAN DEFAULT 0,
    constPointer BOOLEAN DEFAULT 0,
    nonTypeTemplateParameter BOOLEAN DEFAULT 0,
    methodOverloadId INTEGER,
    desc TEXT,
    FOREIGN KEY (methodOverloadId) REFERENCES methodOverload(id)
);

-- 创建 enum 表
CREATE TABLE enum (
    id INTEGER PRIMARY KEY,
    name TEXT NOT NULL,
    headerId INTEGER,
    namespaceId INTEGER,
    classId INTEGER,
    desc TEXT,
    FOREIGN KEY (headerId) REFERENCES header(id),
    FOREIGN KEY (namespaceId) REFERENCES namespace(id),
    FOREIGN KEY (classId) REFERENCES class(id)
);

-- 创建 enumValue 表
CREATE TABLE enumValue (
    id INTEGER PRIMARY KEY,
    name TEXT NOT NULL,
    value INTEGER NOT NULL,
    enumId INTEGER,
    desc TEXT,
    FOREIGN KEY (enumId) REFERENCES enum(id)
);

-- 创建 globalDefinition 表
CREATE TABLE globalDefinition (
    id INTEGER PRIMARY KEY,
    name TEXT NOT NULL,
    type TEXT NOT NULL,
    value TEXT,
    headerId INTEGER,
    namespaceId INTEGER,
    desc TEXT,
    FOREIGN KEY (headerId) REFERENCES header(id),
    FOREIGN KEY (namespaceId) REFERENCES namespace(id)
);

-- 创建 function 表
CREATE TABLE function (
    id INTEGER PRIMARY KEY,
    name TEXT,
    returnType TEXT,
    returnDesc TEXT,
    constReturn BOOLEAN DEFAULT 0,
    inline BOOLEAN DEFAULT 0,
    noexcept BOOLEAN DEFAULT 0,
    constexpr BOOLEAN DEFAULT 0,
    static BOOLEAN DEFAULT 0,
    nodiscard BOOLEAN DEFAULT 0,
    templateFunc BOOLEAN DEFAULT 0,
    desc TEXT
);

-- 创建 functionOverload 表
CREATE TABLE functionOverload (
    id INTEGER PRIMARY KEY,
    functionId INTEGER,
    desc TEXT,
    FOREIGN KEY (functionId) REFERENCES function(id)
);

-- 创建 functionOverloadParameter 表
CREATE TABLE functionOverloadParameter (
    id INTEGER PRIMARY KEY,
    name TEXT NOT NULL,
    type TEXT NOT NULL,
    const BOOLEAN DEFAULT 0,
    pointer BOOLEAN DEFAULT 0,
    reference BOOLEAN DEFAULT 0,
    constPointer BOOLEAN DEFAULT 0,
    nonTypeTemplateParameter BOOLEAN DEFAULT 0,
    functionOverloadId INTEGER,
    desc TEXT,
    FOREIGN KEY (functionOverloadId) REFERENCES functionOverload(id)
);

-- 创建 struct 表
CREATE TABLE struct (
    id INTEGER PRIMARY KEY,
    name TEXT NOT NULL,
    namespaceId INTEGER,
    headerId INTEGER,
    classId INTEGER,
    desc TEXT,
    FOREIGN KEY (namespaceId) REFERENCES namespace(id),
    FOREIGN KEY (headerId) REFERENCES header(id),
    FOREIGN KEY (classId) REFERENCES class(id)
);

-- 创建 structField 表
CREATE TABLE structField (
    id INTEGER PRIMARY KEY,
    name TEXT NOT NULL,
    type TEXT NOT NULL,
    const BOOLEAN DEFAULT 0,
    pointer BOOLEAN DEFAULT 0,
    reference BOOLEAN DEFAULT 0,
    constPointer BOOLEAN DEFAULT 0,
    isEnum BOOLEAN DEFAULT 0,
    enumId INTEGER,
    structId INTEGER,
    desc TEXT,
    FOREIGN KEY (structId) REFERENCES struct(id),
    FOREIGN KEY (enumId) REFERENCES enum(id)
);

-- 创建 macro 表
CREATE TABLE macro (
    id INTEGER PRIMARY KEY,
    name TEXT NOT NULL,
    definition TEXT NOT NULL,
    headerId INTEGER,
    desc TEXT,
    FOREIGN KEY (headerId) REFERENCES header(id)
);

-- 创建 macroFunction 表
CREATE TABLE macroFunction (
    id INTEGER PRIMARY KEY,
    name TEXT NOT NULL,
    desc TEXT,
    headerId INTEGER,
    FOREIGN KEY (headerId) REFERENCES header(id)
);

-- 创建 macroFunctionParameter 表
CREATE TABLE macroFunctionParameter (
    id INTEGER PRIMARY KEY,
    name TEXT NOT NULL,
    desc TEXT,
    macroFunctionId INTEGER,
    FOREIGN KEY (macroFunctionId) REFERENCES macroFunction(id)
);
