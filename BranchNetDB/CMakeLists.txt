project(BranchNetDB LANGUAGES Java)

set(SOURCES
  BRanchNetDB.java
)

add_java_jar_target(BranchNetDB "${SOURCES}" BranchNetDB)